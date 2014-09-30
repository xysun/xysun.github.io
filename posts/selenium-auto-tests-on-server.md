<!-- 
.. link: 
.. description: 
.. tags: Code, selenium
.. date: 2014/09/21 13:11:27
.. title: Running automated selenium tests on Debian server
.. slug: selenium-auto-tests-on-server
-->

In the startup I work we use [selenium](http://www.seleniumhq.org/) for automated web testing. Previously the testing flow involves manually open the selenium plugin in firefox, set the environment-specific variables and click the "Play entire test suite" button. Recently I migrated the task to a Debian server which frees us from manual work and enables us to do much more, eg. test scheduling, integrating to the deployment process, etc. 

Below is my notes on achieving this.

### Running selenium headless

Selenium normally requires a display. On a Linux server, I use [xvfb](http://www.x.org/archive/X11R7.7/doc/man/man1/Xvfb.1.xhtml) to simulate the display. The command is:

```bash
xvfb-run --server-num=10 <python commands here>
```

eg:

```bash
xvfb-run --server-num=10 python -m unittest discover --pattern=*.py
```

I have also found out that the method `driver.find_element_by_link_text` often fails with the message "cannot scroll into view". I have tried the fix with `maximize_window` and `window.scrollTo(x, y)` but with no luck. In the end I have to replace some of the clicks with `driver.get(url)` calls. 

### POST request in logged in session

Selenium does not support POST request naturally (if it does please let me know!), this is a bit of headache when some jquery POST calls need to be tested within a logged in session. 

Luckily we use cookies to verify logged in sessions and thus can have the super awesome [requests](http://docs.python-requests.org/en/latest/) library to the rescue.

The tricky part(I am not sure if it is a universal case) is that when I login through POST request to our authenticate service, the returned cookie cannot be found in the `request.cookies`, rather it is part of the `r.request.headers['Cookie']`. 

So for example to test the `like` button in a post (something similar to the facebook `like` button):

```python

# step1 login
r = request.post(authen_url, data = {'username': username, 'password': password})

# get cookie from headers 
# note r.request.headers['Cookie'] is a comma separated string
# eg: name1=val1; name2=val2
cookie_str = r.request.headers['Cookie']
cookies = {}
for c in cookie_str.split(';'):
    cname = c.split('=')[0]
    cval = c.split('=')[1]
    cookies[cname] = cval

# step2 test other functions with the obtained cookie
# eg. a "like" POST function here
u = 'domain.com/service/likefeed'
d = {'feedid':feedid}
r = requests.post(u, data = d, cookies = cookies)

# step3 use selenium to test frontend effect, eg. a thumbs up symbol
# selenium webdriver functions here

```

### Supporting different environments

We have 3 sets of development environments (demo --> pilot --> production). To switch among these 3 seamlessly, I created a `config.ini` file writing down the environment-specific variables (eg. base URL, test account login, test post contents, etc.) for each environment, and pass this config (using the excellent [configobj](https://pypi.python.org/pypi/configobj) module) when initializing each test suite:

So I have a utility function like below:

```python

#utils.py

from configobj import ConfigObj

def read_env(env):
    c = ConfigObj('config.ini')
    return env, c[env + '_baseurl'], c[env + '_account'], c[env + '_passwd']

```

Then in each test suite script I add a customized `__init__` call that will initialize properties to respective values according to the passed environment, these values can then be used in later testing functions:

For example:

```python

# test_suite_1.py

from utils import read_env

class TestSuite1(unittest.TestCase):
    def __init__(self, env):
        super(TestSuite1, self).__init__('test_case_1')
        self.env, self.base_url, self.account, self.passwd = read_env(en)

```

Then I have a master file controlling the complete test run:

```python
# run.py

from test_suite_1 import TestSuite1

if __name__ == '__main__':
    env = sys.argv[1]
    suite = unittest.TestSuite()

    suite.addTest(TestSuite1(env)) # initialize with specific env

    unittest.TextTestRunner().run(suite)

```

To make things easier I also add a bash alias in `.bash_aliases`:

```bash
alias mftest = 'xvfb-run --server-num=10 python /path/to/tests/run.py'
```

In this way the complete test process can be kicked off by simply running `mftest <env>` where `<env>` can be either `demo` or `pilot` or `prod`, and the program will pick up the right config.

