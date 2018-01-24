# PyTest_Cheatsheet
**Most important PyTest knowledge at a glance**

### Basic Testing
```python
def test_demotest():
  assert True
  assert 1 == 1
  assert 2>=1
  somelist = [1, 2]
  assert 1 in somelist
  assert False, 'This test failed because it had to...'
```
The failuretext is especially useful if used with ``` --tb=line ```

### Options

``` -k 'test_demotest' ``` Runs all tests whose names contain 'test_demotest'</br>
``` -m 'demomarker and not othermarker' ``` Runs all tests marked with "demomarker" which do not have a "othermarker" on them </br>
``` -x ``` Exits on first error </br>
``` --maxfail=2 ``` Exits after 2 errors </br>
``` --lf ``` Reruns only **l**ast **f**ailed test </br>
``` --ff ``` Reruns the **l**ast **f**ailed tests first </br>
``` -v ``` Inrease verbosity </br>
``` -q ``` Decrease verbosity (quiet) </br>
``` --duration=10 ``` Shows slowest 10 tests </br>
``` --collect-only ``` Collects tests, but does not execute them (to check if the options are set right) </br>
``` -h ``` Shows help </br>

``` --tb=long ```  The default traceback formatting </br>
``` --tb=native ```  The Python standard library formatting </br>
``` --tb=short ```  A shorter traceback format </br>
``` --tb=line ```  Only one line per failure </br>
``` --tb=no ```  No tracebak output </br>


### Marking

#### Custom Marker

```python
@pytest.mark.demomarker
def test_demotest():
  assert True
```
Running the test with ``` -m 'demomarker' ``` will only execute tests which have the ```@pytest.mark.demomarker```-Marker

#### Skipping Test
Skipps test and shows reason when using the verbose ```-v``` option for the output:
```python
@pytest.mark.skip(reason='This is the reason for skipping this test')
def test_demotest():
  assert True
```

Skipps test if some **requirement** is met (like checking the version of the class, V1.0 does not support the tested feature but V1.1 does):
```python
@pytest.mark.skipif(someClass.__version__<'1.0')
def test_demotest():
  assert True
```

#### Expect Failure
```python
@pytest.mark.xfail()
def test_demotest():
  assert False
```
Expect failure until some **requirement** is met (like checking the version of the class):
```python
@pytest.mark.xfail(someClass.__version__<'1.0',reason='not suported until V1.1')
def test_demotest():
  assert False
```

#### Parameterized Tests
Using a list of strings to **run a test multiple times with different parameters**:
```python
@pytest.mark.parametrize('strings',['string1','string2'])
def test_demotest(strings):
  assert 'string1' == strings
```

Using a list of tuples to run a test multiple times with different parameters:
```python
@pytest.mark.parametrize('city,code',[('zurich',8050),('basel',4001)])
def test_demotest(city,code):
  assert city == 'zurich'
  assert code == 8050
```

### Fixtures
Fixtures are bits of code that run **before** and **after** the tests. They can be used to **prepare sets of data** which are needed by multiple tests or to **initialize and close a database** connection:
```python
@pytest.fixture()
def demofixture():
    return 'demodata'

def test_some_data(demofixture):
    assert demofixture == 'demodata'
```

Sharing Fixtures among **multiple files**: define fixture in the **conftext.py** file to make it available in this directory and all subdirectories.

Fixtures with DB-connections:
```python
@pytest.fixture()
def someFixture():
    startDB()
    
    yield
    
    stopDB()
```
Fixtures can be used by other fixtures. Also multiple fixtures can be used.

The **Scope** of a fixture can be definded additionaly:</br>
```@pytest.fixture(scope='function')``` Runs once per each test function (**default**)</br>
```@pytest.fixture(scope='class')``` Runs once per test class</br>
```@pytest.fixture(scope='module')``` Runs once per module</br>
```@pytest.fixture(scope='session')``` Runs once per session</br>

Autouse a Fixture: ```@pytest.fixture(autouse=True)``` - Fixture gets run on every test. For example a timer function which determines the runtime of each test:</br>
```python
@pytest.fixture(autouse=True)
def timerFixture():
    start = time.time()
    yield
    delta = time.time() - start
    print('\ntest duration : {:0.3} seconds'.format(delta))
 ```
 
Renaming fixtures:
```python
@pytest.fixture(name='fix')
def demofixture():
    return 'demodata'
 ```
 
Parametrizing Fixtures:
```python
demolist = ['string1','string2']

@pytest.fixture(params=demolist)
def demofixture(request):
    return request.params
 ```
 #### tempdir and tempdir_factory
 These fixures are used to create and delete temporary directories and files used by tests. Tempdir has a function scope:
 ```python
def test_tmpdir(tmpdir):
    a_file = tmpdir.join('some_file.txt')
    sub_dir = tmpdir.mkdir('some_sub_dir')
    another_file = a_sub_dir.join('someother_file.txt')
    
    a_file.write('we write to some_file')
    another_file.write('we write to someother_file')
    
    assert a_file.read() == 'we write to some_file'
    assert another_file.read() == 'we write to someother_file'
 ```
 
 Tempdir_factory has a session scope:
  ```python
def test_tmpdir(tmpdir_factory):
    a_dir = tmpdir_factory.mktemp('some_dir')

    a_file = a_dir.join('some_file.txt')
    sub_dir = a_dir.mkdir('some_sub_dir')
    another_file = a_sub_dir.join('someother_file.txt')
    
    a_file.write('we write to some_file')
    another_file.write('we write to someother_file')
    
    assert a_file.read() == 'we write to some_file'
    assert another_file.read() == 'we write to someother_file'
 ```
 
 
 
 
