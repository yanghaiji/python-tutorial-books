## 配置文件pytest.ini的详细使用

pytest配置文件可以改变pytest的运行方式，它是一个固定的文件pytest.ini文件，读取配置信息，按指定的方式去运行

## 非test文件

pytest里面有些文件是非test文件

- pytest.ini：pytest的主配置文件，可以改变pytest的默认行为
- conftest.py：测试用例的一些fixture配置
- _*init*_.py：识别该文件夹为python的package包

## 查看pytest.ini的配置选项

cmd执行

```javascript
pytest --help
```

复制

找到这部分内容

```javascript
[pytest] ini-options in the first pytest.ini|tox.ini|setup.cfg file found:

  markers (linelist):   markers for test functions
  empty_parameter_set_mark (string):
                        default marker for empty parametersets
  norecursedirs (args): directory patterns to avoid for recursion
  testpaths (args):     directories to search for tests when no files or directories are given in the command line.
  usefixtures (args):   list of default fixtures to be used with this project
  python_files (args):  glob-style file patterns for Python test module discovery
  python_classes (args):
                        prefixes or glob names for Python test class discovery
  python_functions (args):
                        prefixes or glob names for Python test function and method discovery
  disable_test_id_escaping_and_forfeit_all_rights_to_community_support (bool):
                        disable string escape non-ascii characters, might cause unwanted side effects(use at your own
                        risk)
  console_output_style (string):
                        console output: "classic", or with additional progress information ("progress" (percentage) |
                        "count").
  xfail_strict (bool):  default for the strict parameter of xfail markers when not given explicitly (default: False)
  enable_assertion_pass_hook (bool):
                        Enables the pytest_assertion_pass hook.Make sure to delete any previously generated pyc cache
                        files.
  junit_suite_name (string):
                        Test suite name for JUnit report
  junit_logging (string):
                        Write captured log messages to JUnit report: one of no|log|system-out|system-err|out-err|all
  junit_log_passing_tests (bool):
                        Capture log information for passing tests to JUnit report:
  junit_duration_report (string):
                        Duration time to report: one of total|call
  junit_family (string):
                        Emit XML for schema: one of legacy|xunit1|xunit2
  doctest_optionflags (args):
                        option flags for doctests
  doctest_encoding (string):
                        encoding used for doctest files
  cache_dir (string):   cache directory path.
  filterwarnings (linelist):
                        Each line specifies a pattern for warnings.filterwarnings. Processed after -W/--pythonwarnings.
  log_print (bool):     default value for --no-print-logs
  log_level (string):   default value for --log-level
  log_format (string):  default value for --log-format
  log_date_format (string):
                        default value for --log-date-format
  log_cli (bool):       enable log display during test run (also known as "live logging").
  log_cli_level (string):
                        default value for --log-cli-level
  log_cli_format (string):
                        default value for --log-cli-format
  log_cli_date_format (string):
                        default value for --log-cli-date-format
  log_file (string):    default value for --log-file
  log_file_level (string):
                        default value for --log-file-level
  log_file_format (string):
                        default value for --log-file-format
  log_file_date_format (string):
                        default value for --log-file-date-format
  log_auto_indent (string):
                        default value for --log-auto-indent
  faulthandler_timeout (string):
                        Dump the traceback of all threads if a test takes more than TIMEOUT seconds to finish. Not
                        available on Windows.
  addopts (args):       extra command line options
  minversion (string):  minimally required pytest version
  rsyncdirs (pathlist): list of (relative) paths to be rsynced for remote distributed testing.
  rsyncignore (pathlist):
                        list of (relative) glob-style paths to be ignored for rsyncing.
  looponfailroots (pathlist):
                        directories to check for changes
```

复制

## pytest.ini应该放哪里？

就放在项目根目录下 ，不要乱放，不要乱起其他名字

**接下来讲下常用的配置项**

## marks

**作用：**测试用例中添加了 @pytest.mark.webtest 装饰器，如果不添加marks选项的话，就会报warnings

**格式：**list列表类型

**写法：**

```javascript
[pytest]
markers =
    weibo: this is weibo page
    toutiao: toutiao
    xinlang: xinlang
```

复制

## xfail_strict

**作用：**设置xfail_strict = True可以让那些标记为@pytest.mark.xfail但实际通过显示XPASS的测试用例被报告为失败

**格式：**True 、False（默认），1、0

**写法：**

```javascript
[pytest]

# mark标记说明
markers =
    weibo: this is weibo page
    toutiao: toutiao
    xinlang: xinlang

xfail_strict = True
```

复制

**具体代码栗子**

未设置 xfail_strict = True 时，测试结果显示XPASS

```javascript
@pytest.mark.xfail()
def test_case1():
    a = "a"
    b = "b"
    assert a != b
```

复制

已设置 xfail_strict = True 时，测试结果显示failed

```javascript
collecting ... collected 1 item

02断言异常.py::test_case1 FAILED                                         [100%]
02断言异常.py:54 (test_case1)
[XPASS(strict)] 

================================== FAILURES ===================================
_________________________________ test_case1 __________________________________
[XPASS(strict)] 
=========================== short test summary info ===========================
FAILED 02断言异常.py::test_case1
============================== 1 failed in 0.02s ==============================
```

复制

## **addopts**

**作用：**addopts参数可以更改默认命令行选项，这个当我们在cmd输入一堆指令去执行用例的时候，就可以用该参数代替了，省去重复性的敲命令工作

**比如：**想测试完生成报告，失败重跑两次，一共运行两次，通过分布式去测试，如果在cmd中写的话，命令会很长

```javascript
pytest -v --rerun=2 --count=2 --html=report.html --self-contained-html -n=auto
```

复制

每次都这样敲不太现实，addopts就可以完美解决这个问题

```javascript
[pytest]

# mark
markers =
    weibo: this is weibo page
    toutiao: toutiao
    xinlang: xinlang

xfail_strict = True

# 命令行参数
addopts = -v --reruns=1 --count=2 --html=reports.html --self-contained-html -n=auto
```

复制

**加了addopts之后，我们在cmd中只需要敲pytest就可以生效了！！**

## log_cli

**作用：**控制台实时输出日志

**格式：**log_cli=True 或False（默认），或者log_cli=1 或 0

### log_cli=0的运行结果

![img](https://ask.qcloudimg.com/http-save/yehe-7440717/gxmla6tf16.png?imageView2/2/w/1620)

### log_cli=1的运行结果

![img](https://ask.qcloudimg.com/http-save/yehe-7440717/2l5tdafnmw.png?imageView2/2/w/1620)

### 结论

很明显，加了log_cli=1之后，可以清晰看到哪个package下的哪个module下的哪个测试用例是否passed还是failed；

**所以平时测试代码是否有问题的情况下推荐加！！！但如果拿去批量跑测试用例的话不建议加，谁知道会不会影响运行性能呢？**

## norecursedirs

**作用：**pytest 收集测试用例时，会递归遍历所有子目录，包括某些你明知道没必要遍历的目录，遇到这种情况，可以使用 norecursedirs 参数简化 pytest 的搜索工作**【还是挺有用的！！！】**

**默认设置：** norecursedirs = .* build dist CVS _darcs {arch} *.egg 

**正确写法：**多个路径用空格隔开

```javascript
[pytest]

norecursedirs = .* build dist CVS _darcs {arch} *.egg venv src resources log report util
```

复制

## 更改测试用例收集规则

pytest默认的测试用例收集规则

- 文件名以 test_*.py 文件和 *_test.py
- 以  test_ 开头的函数
- 以  Test 开头的类，不能包含 __init__ 方法
- 以  test_ 开头的类里面的方法

我们是可以修改或者添加这个用例收集规则的；当然啦，是建议在原有的规则上添加的，如下配置

```javascript
[pytest]

python_files =     test_*  *_test  test*
python_classes =   Test*   test*
python_functions = test_*  test*
```

本文选自 [小菠萝测试笔记](https://cloud.tencent.com/developer/article/1640840)