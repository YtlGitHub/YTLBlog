---
title: 小红书ui自动化测试
date: 2022-11-15 00:02:19
tags: 自动化测试
categories: APP
---
小红书ui自动化测试，运行RUN_main.py文件执行所有测试用例
用到了[uiautomator2](http://e.betheme.net/article/show-80644.html?action=onClick)
[uiautomator2笔记](https://blog.csdn.net/baidu_39372836/article/details/122367121)

## ui自动化框架
先看一下目录结构
![目录结构](http://ytlgithub.github.io/medias/ytlimg/ui自动化目录结构.jpg)
![python命名规则](http://ytlgithub.github.io/medias/ytlimg/python命名规则.jpg)

## config
```yaml
PACKAGE_NAME : com.xingin.xhs  # 包名
MOBILE_IP : 192.168.16.185  # 输入手机ip
CONNECT : usb  # 连接方式两种 USB和WiFi连接
```
## 目录结构内容如下
## data
```data

```

## drivers
```drivers

```

## log
```log

```

## report
```report

```

## script
### common
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: little_red_book
# @Author: 杨天龙
# @Time: 2022/11/8 21:47
import unittest
import uiautomator2 as u2
import time
import os
from utils.yamlconfig import ReadYaml


class MyTest(unittest.TestCase):
    @classmethod
    def setUpClass(self):
        """整个测试用例的前置条件"""
        global PACKAGE_NAME
        config_path = os.path.abspath(os.path.join(os.getcwd(), '../..', 'config/config_file'))  # 配置文件路径
        print(config_path, '配置文件路径')
        PACKAGE_NAME = ReadYaml(config_path, 'PACKAGE_NAME').get_data()  # 包名
        MOBILE_IP = ReadYaml(config_path, 'MOBILE_IP').get_data()  # 手机ip
        a = ReadYaml(config_path, "CONNECT").get_data()  # 获取配置文件连接方式
        if a == "USB" or a == "usb":
            devices_serial = os.popen('adb devices').read().split()[4]  # 获取设备序列号
            print("USB连接", devices_serial)
            self.d = u2.connect(devices_serial)
            print("USB连接成功")
        else:
            try:
                self.d = u2.connect()
                print("配置WIFI连接")
                os.system("adb tcpip 5555")  # 设置手机端口
                MOBILE_IP = MOBILE_IP  # 手机ip
                os.system(f"adb connect {MOBILE_IP}:5555")
                print("配置WIFI连接成功")
            except Exception as e:
                print("无线连接")
                self.d = u2.connect(f"{MOBILE_IP}:5555")  # 用手机IP地址无线连接手机
                print("无线连接成功")
        self.d.click_post_delay = 1.5  # 定义全局等待时间为1.5s
        self.d.implicitly_wait(10)
        # print(self.d.device_info)
        # 录屏操作
        screen_record_path = os.path.abspath(os.path.join(os.getcwd(), '../..', 'report/screen_record'))
        if not os.path.exists(screen_record_path):
            os.mkdir(screen_record_path)
        now = time.time()  # 现在的时间
        now_time = time.strftime("%Y%m%d_%H%M%S", time.localtime(now))  # 当前时间
        self.d.screenrecord(screen_record_path+f"/视频录制{now_time}.mp4")
        print(screen_record_path, '录屏保存目录')
        # 抓取手机日志并上传到电脑log目录
        os.system("adb logcat -c")  # 抓取日志前，清除旧的日志信息
        logcat_name = f'/logcat{now_time}.txt'
        logcat_path = os.path.abspath(os.path.join(os.getcwd(), '../..', 'log'))
        if not os.path.exists(logcat_path):
            os.mkdir(logcat_path)
            print("生成log目录成功：", logcat_path)
        logcat = f"adb logcat -v time > {logcat_path}"
        print(logcat, "logcat")
        os.popen(logcat + logcat_name)  # 抓取日志

        print(self.d.current_app(), "当前运行的应用")

    @classmethod
    def tearDownClass(self):
        """print("所有用例的后置条件")"""
        # self.d.screenrecord.stop()  # 停止录屏
        process = os.popen("adb shell ps -A | findstr logcat")  # 获取logcat进程
        try:
            process_id = process.read().split()[1]  # 获取logcat进程号
            os.popen(f"adb shell kill {process_id}")  # 杀掉logcat进程
        except Exception as e:
            print(e)

    def setUp(self):
        """print("每条用例的前置条件")"""
        self.d.app_start(PACKAGE_NAME)

    def tearDown(self):
        """print("每条用例的后置条件")"""
        self.d.app_stop(PACKAGE_NAME)

    def errorSaveScreenAsPhoto(self, error_module_name):
        now = time.time()  # 现在的时间
        now_time = time.strftime("%Y%m%d_%H%M%S", time.localtime(now))  # 当前时间
        error_screenshot_name = f'{error_module_name}错误截图{now_time}.png'
        error_screenshot_path = f"{os.path.abspath(os.path.join(os.getcwd(), '../..'))}/report/images/"
        if not os.path.exists(error_screenshot_path):
            os.mkdir(error_screenshot_path)
            print("生成错误截图目录成功：", error_screenshot_path)
        self.d.screenshot(error_screenshot_path + error_screenshot_name)  # 错误截图保存路径
        msg = f'<img src="./images/{error_screenshot_name}" width="400" height="800" title="{error_screenshot_name}" alt="{error_screenshot_name}"/>'
        print(msg)

    def assertEqualErrorSaveScreenAsPhoto(self, expected_results, actual_result, error_module_name):
        """断言错误保存屏幕照片"""
        try:
            self.assertEqual(expected_results, actual_result, msg="实际结果和预期结果不一致")
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        else:
            'print("断言成功")'
            pass

    def assertTrueEqualErrorSaveScreenAsPhoto(self, actual_result, error_module_name):
        """断言错误保存屏幕照片"""
        try:
            self.assertTrue(actual_result, msg="没有找到预期结果元素")
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        else:
            'print("断言成功")'
            pass
```
### page
```python

```

### suit
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: RUN
# @Author: 杨天龙
# @Time: 2022/11/8 23:08
# 下载unittestreport：pip install unittestreport 生成测试报告和发邮件用，还可以做数据驱动
import time
import unittest
from unittestreport import TestRunner
import os
import sys
# 把当前项目路径临时添加到环境变量里，执行当前文件的窗口中有效，窗口关闭后即失效。
sys.path.append(os.path.abspath(os.path.join(os.getcwd(), '../..')))
from utils.HTMLTestRunner import HTMLTestRunner


class RunAmin():
    def run_main_unittestreport(self, pattern="text*.py"):
        # case_path = os.getcwd()  # 获取当前路径
        case_path = f"{os.path.abspath(os.path.join(os.getcwd(), '..'))}/test_case/"  # 获取用例路径
        print(case_path, "用例路径")  # 打印用例路径
        #  加载套件
        suite = unittest.defaultTestLoader.discover(case_path, pattern=pattern)
        print("测试套件总用例数：", suite.countTestCases())

        now = time.time()  # 现在的时间
        now_time = time.strftime("%Y%m%d_%H%M%S", time.localtime(now))  # 当前时间
        report_name = f"测试报告{now_time}.html"  # 报告名后面加当前时间
        report_path = f'{os.path.abspath(os.path.join(os.getcwd(), "../.."))}/report/'  # 生成报告路径
        if not os.path.exists(report_path):
            print("生成报告目录")
            os.mkdir(report_path)
            print("生成报告目录成功:", report_path)
        print(report_path, "报告路径")  # 打印报告路径
        runner = TestRunner(suite,
                            filename=report_name,
                            report_dir=report_path,
                            title="小红书ui自动化测试报告",
                            tester="杨天龙",
                            desc="小红书首次登录，登入之后一级功能点检",
                            templates=2
                            )
        runner.rerun_run(count=0, interval=2)  # count：用来指定用例失败重运行的次数 interval：指定每次重运行的时间间隔

        # 邮件发送测试报告
        # runner.send_email(host="smtp.qq.com",
        #                   port=465,  # 587
        #                   user="176@qq.com",
        #                   password="xxtdlejeiatfcdhd",
        #                   to_addrs=["176@qq.com", "1632008076@qq.com"]
        #                   )

        """
        参数介绍 
        host： smtp服务器地址
        port：端口
        user：邮箱账号
        password：smtp服务授权码
        to_addrs：收件人邮箱地址（一个收件人传字符串，多个收件人传列表）
        """
    def run_main_htmltestrunner(self, pattern="text*.py"):
        now = time.time()  # 现在的时间
        now_time = time.strftime("%Y%m%d_%H%M%S", time.localtime(now))  # 当前时间
        report_name = f"测试报告{now_time}.html"  # 报告名后面加当前时间
        report_path = f'{os.path.abspath(os.path.join(os.getcwd(), "../.."))}/report/'  # 生成报告路径
        if not os.path.exists(report_path):
            print("生成报告目录")
            os.mkdir(report_path)
            print("生成报告目录成功:", report_path)
        with open(report_path+report_name, "wb") as fl:
            case_path = f"{os.path.abspath(os.path.join(os.getcwd(), '..'))}/test_case/"  # 获取用例路径
            print(case_path, "用例路径")  # 打印用例路径
            discover = unittest.defaultTestLoader.discover(start_dir=case_path, pattern=pattern)
            runner = HTMLTestRunner(title='测试标题', description='描述本次测试的大概内容', stream=fl)
            runner.run(discover)


if __name__ == '__main__':
    RunAmin().run_main_unittestreport("test_*.py")
```

### test_case
#### test_00_first_open_app
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: test_00_first_open_app
# @Author: 杨天龙
# @Time: 2022/11/9 0:23
import time
from script.common.little_red_book import MyTest


class FirstOpenApp(MyTest):
    """首次打开app类"""
    def test_01(self, error_module_name="首次打开放弃使用"):
        """首次打开放弃使用"""
        try:
            self.d.app_clear("com.xingin.xhs")  # 清理数据
            self.d.app_start("com.xingin.xhs")  # 启动app
            time.sleep(1)
            self.d(text="不同意").click(timeout=3)  # 点击不同意
            self.d(text="放弃使用").click(timeout=3)  # 点击放弃使用
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        self.assertTrue(True)

    def test_02(self, error_module_name="首次打开同意"):
        """首次打开同意"""
        try:
            self.d(text="同意").click()  # 点击同意
            if self.d(text="其他账号").exists(timeout=3):
                self.d(text="其他账号").click(timeout=3)
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        actual_result = self.d(text="其他登录方式").get_text()  # 获取预期结果
        self.assertEqualErrorSaveScreenAsPhoto("其他登录方式", actual_result, error_module_name)

    def test_03(self, error_module_name="其他登录输入错误的验证码"):
        """其他登录输入错误的验证码"""
        try:
            if self.d(text="其他账号").exists(timeout=3):
                self.d(text="其他账号").click()
            self.d(text="其他登录方式").click()  # 点击登录方式
            self.d(resourceId="com.xingin.xhs:id/abr").click()  # 点击手机图标
            self.d(resourceId="com.xingin.xhs:id/dfv").send_keys("17664014893")  # 输入账号
            # self.d(resourceId="com.xingin.xhs:id/adx").click(timeout=5)  # 点击获取验证码
            self.d(resourceId="com.xingin.xhs:id/emt").click(timeout=3)  # 点击"我已阅读并同意"前面那个小圆点
            self.d(resourceId="com.xingin.xhs:id/ae1").send_keys("123456")  # 输入错误验证码
            time.sleep(3)
            self.d(text="登录").click()  # 点击登录
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        actual_result = self.d(text="登录").get_text()  # 获取预期结果
        self.assertEqualErrorSaveScreenAsPhoto("登录", actual_result, error_module_name)

    def test_04(self, error_module_name="微信登录"):
        """微信登录"""
        try:
            if self.d(text="其他账号").exists(timeout=3):
                self.d(text="其他账号").click()
            self.d(resourceId="com.xingin.xhs:id/emt").click(timeout=3)  # 点击"我已阅读并同意"前面那个小圆点
            self.d(text="微信登录").click(timeout=3)  # 点击微信登录
            if self.d(text="允许").exists:
                self.d(text="允许").click(timeout=3)  # 点击允许
                self.d(text="跳过").click(timeout=3)  # 点击跳过
            self.d.swipe_ext("up")  # 向上滑动
            time.sleep(3)
            actual_result = self.d(text="推荐").get_text(timeout=5)  # 获取实际结果
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        self.assertEqualErrorSaveScreenAsPhoto("推荐", actual_result, error_module_name)
```
#### test_01_home_page
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: test_01_home_page
# @Author: 杨天龙
# @Time: 2022/11/8 22:21


from script.common.little_red_book import MyTest
import time


class HomePage(MyTest):
    def test_01(self, error_module_name="检查首页tab"):
        """检查首页tab"""
        try:
            time.sleep(1)
            if self.d(text="允许").exists(timeout=3):
                self.d(text="允许").click(timeout=3)  # 点击允许
            self.d(text="首页").click()  # 点击首页
            actual_result = self.d(text="推荐").get_text()  # 获取预期结果
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        self.assertEqualErrorSaveScreenAsPhoto("推荐", actual_result, error_module_name)
```
#### test_02_look_look
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: test_02_look_look
# @Author: 杨天龙
# @Time: 2022/11/8 22:48
from script.common.little_red_book import MyTest


class LookLook(MyTest):
    def test_01(self, error_module_name="检查视频/看看/购物tab"):
        """检查视频/看看/购物tab"""
        try:
            actual_result = "实际结果True或Fail"
            if self.d(text="允许").exists(timeout=3):
                self.d(text="允许").click(timeout=3)  # 点击允许
            if self.d(text="视频").exists:
                self.d(text="视频").click()  # 点击视频
                actual_result = self.d(text="视频").exists  # 获取预期结果
            if self.d(text="看看").exists:
                self.d(text="看看").click()  # 点击看看
                actual_result = self.d(text="看看").exists  # 获取预期结果
            if self.d(text="购物").exists:
                self.d(text="购物").click()  # 点击购物
                actual_result = self.d(text="购物").exists  # 获取预期结果
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        self.assertTrueEqualErrorSaveScreenAsPhoto(actual_result, error_module_name)
```
#### test_03_test_news
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: test_03_test_news
# @Author: 杨天龙
# @Time: 2022/11/8 23:33
from script.common.little_red_book import MyTest


class News(MyTest):
    """检查消息"""
    def test_01(self, error_module_name="检查消息"):
        """检查消息"""
        try:
            if self.d(text="允许").exists(timeout=3):
                self.d(text="允许").click(timeout=3)  # 点击允许
            self.d(text="消息").click()  # 点击消息
            actual_result = self.d(text="创建聊天").get_text()  # 获取预期结果
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        self.assertEqualErrorSaveScreenAsPhoto("创建聊天", actual_result, error_module_name)
```
#### test_04_my
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: test_04_my
# @Author: 杨天龙
# @Time: 2022/11/8 23:41
from script.common.little_red_book import MyTest


class My(MyTest):
    """检查我tab"""
    def test_01(self, error_module_name="检查我tab"):
        """检查我tab"""
        try:
            if self.d(text="允许").exists(timeout=3):
                self.d(text="允许").click(timeout=3)  # 点击允许
            self.d(text="我").click()  # 点击我tab
            actual_result = self.d(text="关注").get_text()  # 获取预期结果
        except Exception as e:
            self.errorSaveScreenAsPhoto(error_module_name)
            raise e
        self.assertEqualErrorSaveScreenAsPhoto("关注", actual_result, error_module_name)
```
## utils
### get_device_ip
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: get_device_ip
# @Author: 杨天龙
# @Time: 2022/11/11 0:43
import os
import re


def exec_cmd(cmd):
    cmd = os.popen(cmd)
    cmd_result = cmd.read()
    cmd.close()
    return cmd_result


def get_device_ip(content):
    math_obj = re.search(r'inet\s(\d+\.\d+\.\d+\.\d+).*?wlan0', content)
    if math_obj and math_obj.group(1):
        return math_obj.group(1)
    return None


if __name__ == '__main__':
    result1 = exec_cmd('adb shell ip addr show wlan0')
    ip = get_device_ip(result1)  # 获得手机IP地址
    print(ip)
```
### HTMLTestRunner
```python
"""
A TestRunner for use with the Python unit testing framework. It
generates a HTML report to show the result at a glance.

The simplest way to use this is to invoke its main method. E.g.

    import unittest
    import HTMLTestRunner

    ... define your tests ...

    if __name__ == '__main__':
        HTMLTestRunner.main()


For more customization options, instantiates a HTMLTestRunner object.
HTMLTestRunner is a counterpart to unittest's TextTestRunner. E.g.

    # output to a file
    fp = file('my_report.html', 'wb')
    runner = HTMLTestRunner.HTMLTestRunner(
                stream=fp,
                title='My unit test',
                description='This demonstrates the report output by HTMLTestRunner.'
                )

    # Use an external stylesheet.
    # See the Template_mixin class for more customizable options
    runner.STYLESHEET_TMPL = '<link rel="stylesheet" href="my_stylesheet.css" type="text/css">'

    # run the test
    runner.run(my_test_suite)


------------------------------------------------------------------------
Copyright (c) 2004-2007, Wai Yip Tung
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are
met:

* Redistributions of source code must retain the above copyright notice,
  this list of conditions and the following disclaimer.
* Redistributions in binary form must reproduce the above copyright
  notice, this list of conditions and the following disclaimer in the
  documentation and/or other materials provided with the distribution.
* Neither the name Wai Yip Tung nor the names of its contributors may be
  used to endorse or promote products derived from this software without
  specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS
IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED
TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A
PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER
OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
"""

# URL: http://tungwaiyip.info/software/HTMLTestRunner.html

__author__ = "Wai Yip Tung"
__version__ = "0.8.2"


"""
Change History

Version 0.8.2
* Show output inline instead of popup window (Viorel Lupu).

Version in 0.8.1
* Validated XHTML (Wolfgang Borgert).
* Added description of test classes and test cases.

Version in 0.8.0
* Define Template_mixin class for customization.
* Workaround a IE 6 bug that it does not treat <script> block as CDATA.

Version in 0.7.1
* Back port to Python 2.3 (Frank Horowitz).
* Fix missing scroll bars in detail log (Podi).
"""

# TODO: color stderr
# TODO: simplify javascript using ,ore than 1 class in the class attribute?

import datetime
import io  # import StringIO
import sys
import time
import unittest
from xml.sax import saxutils


# ------------------------------------------------------------------------
# The redirectors below are used to capture output during testing. Output
# sent to sys.stdout and sys.stderr are automatically captured. However
# in some cases sys.stdout is already cached before HTMLTestRunner is
# invoked (e.g. calling logging.basicConfig). In order to capture those
# output, use the redirectors for the cached stream.
#
# e.g.
#   >>> logging.basicConfig(stream=HTMLTestRunner.stdout_redirector)
#   >>>

class OutputRedirector(object):
    """ Wrapper to redirect stdout or stderr """
    def __init__(self, fp):
        self.fp = fp

    def write(self, s):
        self.fp.write(bytes(s, 'UTF-8'))  # self.fp.write(s)

    def writelines(self, lines):
        self.fp.writelines(lines)

    def flush(self):
        self.fp.flush()

stdout_redirector = OutputRedirector(sys.stdout)
stderr_redirector = OutputRedirector(sys.stderr)



# ----------------------------------------------------------------------
# Template

class Template_mixin(object):
    """
    Define a HTML template for report customerization and generation.

    Overall structure of an HTML report

    HTML
    +------------------------+
    |<html>                  |
    |  <head>                |
    |                        |
    |   STYLESHEET           |
    |   +----------------+   |
    |   |                |   |
    |   +----------------+   |
    |                        |
    |  </head>               |
    |                        |
    |  <body>                |
    |                        |
    |   HEADING              |
    |   +----------------+   |
    |   |                |   |
    |   +----------------+   |
    |                        |
    |   REPORT               |
    |   +----------------+   |
    |   |                |   |
    |   +----------------+   |
    |                        |
    |   ENDING               |
    |   +----------------+   |
    |   |                |   |
    |   +----------------+   |
    |                        |
    |  </body>               |
    |</html>                 |
    +------------------------+
    """

    STATUS = {
    0: 'pass',
    1: 'fail',
    2: 'error',
    }

    DEFAULT_TITLE = 'Unit Test Report'
    DEFAULT_DESCRIPTION = ''

    # ------------------------------------------------------------------------
    # HTML Template

    HTML_TMPL = r"""<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title>%(title)s</title>
    <meta name="generator" content="%(generator)s"/>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    %(stylesheet)s
</head>
<body>
<script language="javascript" type="text/javascript"><!--
output_list = Array();

/* level - 0:Summary; 1:Failed; 2:All */
function showCase(level) {
    trs = document.getElementsByTagName("tr");
    for (var i = 0; i < trs.length; i++) {
        tr = trs[i];
        id = tr.id;
        if (id.substr(0,2) == 'ft') {
            if (level < 1) {
                tr.className = 'hiddenRow';
            }
            else {
                tr.className = '';
            }
        }
        if (id.substr(0,2) == 'pt') {
            if (level > 1) {
                tr.className = '';
            }
            else {
                tr.className = 'hiddenRow';
            }
        }
    }
}


function showClassDetail(cid, count) {
    var id_list = Array(count);
    var toHide = 1;
    for (var i = 0; i < count; i++) {
        tid0 = 't' + cid.substr(1) + '.' + (i+1);
        tid = 'f' + tid0;
        tr = document.getElementById(tid);
        if (!tr) {
            tid = 'p' + tid0;
            tr = document.getElementById(tid);
        }
        id_list[i] = tid;
        if (tr.className) {
            toHide = 0;
        }
    }
    for (var i = 0; i < count; i++) {
        tid = id_list[i];
        if (toHide) {
            document.getElementById('div_'+tid).style.display = 'none'
            document.getElementById(tid).className = 'hiddenRow';
        }
        else {
            document.getElementById(tid).className = '';
        }
    }
}


function showTestDetail(div_id){
    var details_div = document.getElementById(div_id)
    var displayState = details_div.style.display
    // alert(displayState)
    if (displayState != 'block' ) {
        displayState = 'block'
        details_div.style.display = 'block'
    }
    else {
        details_div.style.display = 'none'
    }
}


function html_escape(s) {
    s = s.replace(/&/g,'&amp;');
    s = s.replace(/</g,'&lt;');
    s = s.replace(/>/g,'&gt;');
    return s;
}

/* obsoleted by detail in <div>
function showOutput(id, name) {
    var w = window.open("", //url
                    name,
                    "resizable,scrollbars,status,width=800,height=450");
    d = w.document;
    d.write("<pre>");
    d.write(html_escape(output_list[id]));
    d.write("\n");
    d.write("<a href='javascript:window.close()'>close</a>\n");
    d.write("</pre>\n");
    d.close();
}
*/
--></script>

%(heading)s
%(report)s
%(ending)s

</body>
</html>
"""
    # variables: (title, generator, stylesheet, heading, report, ending)


    # ------------------------------------------------------------------------
    # Stylesheet
    #
    # alternatively use a <link> for external style sheet, e.g.
    #   <link rel="stylesheet" href="$url" type="text/css">

    STYLESHEET_TMPL = """
<style type="text/css" media="screen">
body        { font-family: verdana, arial, helvetica, sans-serif; font-size: 80%; }
table       { font-size: 100%; }
pre         { }

/* -- heading ---------------------------------------------------------------------- */
h1 {
	font-size: 16pt;
	color: gray;
}
.heading {
    margin-top: 0ex;
    margin-bottom: 1ex;
}

.heading .attribute {
    margin-top: 1ex;
    margin-bottom: 0;
}

.heading .description {
    margin-top: 4ex;
    margin-bottom: 6ex;
}

/* -- css div popup ------------------------------------------------------------------------ */
a.popup_link {
}

a.popup_link:hover {
    color: red;
}

.popup_window {
    display: none;
    position: relative;
    left: 0px;
    top: 0px;
    /*border: solid #627173 1px; */
    padding: 10px;
    background-color: #E6E6D6;
    font-family: "Lucida Console", "Courier New", Courier, monospace;
    text-align: left;
    font-size: 8pt;
    width: 500px;
}

}
/* -- report ------------------------------------------------------------------------ */
#show_detail_line {
    margin-top: 3ex;
    margin-bottom: 1ex;
}
#result_table {
    width: 80%;
    border-collapse: collapse;
    border: 1px solid #777;
}
#header_row {
    font-weight: bold;
    color: white;
    background-color: #777;
}
#result_table td {
    border: 1px solid #777;
    padding: 2px;
}
#total_row  { font-weight: bold; }
.passClass  { background-color: #6c6; }
.failClass  { background-color: #c60; }
.errorClass { background-color: #c00; }
.passCase   { color: #6c6; }
.failCase   { color: #c60; font-weight: bold; }
.errorCase  { color: #c00; font-weight: bold; }
.hiddenRow  { display: none; }
.testcase   { margin-left: 2em; }


/* -- ending ---------------------------------------------------------------------- */
#ending {
}

</style>
"""



    # ------------------------------------------------------------------------
    # Heading
    #

    HEADING_TMPL = """<div class='heading'>
<h1>%(title)s</h1>
%(parameters)s
<p class='description'>%(description)s</p>
</div>

""" # variables: (title, parameters, description)

    HEADING_ATTRIBUTE_TMPL = """<p class='attribute'><strong>%(name)s:</strong> %(value)s</p>
""" # variables: (name, value)



    # ------------------------------------------------------------------------
    # Report
    #

    REPORT_TMPL = """
<p id='show_detail_line'>Show
<a href='javascript:showCase(0)'>Summary</a>
<a href='javascript:showCase(1)'>Failed</a>
<a href='javascript:showCase(2)'>All</a>
</p>
<table id='result_table'>
<colgroup>
<col align='left' />
<col align='right' />
<col align='right' />
<col align='right' />
<col align='right' />
<col align='right' />
</colgroup>
<tr id='header_row'>
    <td>Test Group/Test case</td>
    <td>Count</td>
    <td>Pass</td>
    <td>Fail</td>
    <td>Error</td>
    <td>View</td>
</tr>
%(test_list)s
<tr id='total_row'>
    <td>Total</td>
    <td>%(count)s</td>
    <td>%(Pass)s</td>
    <td>%(fail)s</td>
    <td>%(error)s</td>
    <td>&nbsp;</td>
</tr>
</table>
""" # variables: (test_list, count, Pass, fail, error)

    REPORT_CLASS_TMPL = r"""
<tr class='%(style)s'>
    <td>%(desc)s</td>
    <td>%(count)s</td>
    <td>%(Pass)s</td>
    <td>%(fail)s</td>
    <td>%(error)s</td>
    <td><a href="javascript:showClassDetail('%(cid)s',%(count)s)">Detail</a></td>
</tr>
""" # variables: (style, desc, count, Pass, fail, error, cid)


    REPORT_TEST_WITH_OUTPUT_TMPL = r"""
<tr id='%(tid)s' class='%(Class)s'>
    <td class='%(style)s'><div class='testcase'>%(desc)s</div></td>
    <td colspan='5' align='center'>

    <!--css div popup start-->
    <a class="popup_link" onfocus='this.blur();' href="javascript:showTestDetail('div_%(tid)s')" >
        %(status)s</a>

    <div id='div_%(tid)s' class="popup_window">
        <div style='text-align: right; color:red;cursor:pointer'>
        <a onfocus='this.blur();' onclick="document.getElementById('div_%(tid)s').style.display = 'none' " >
           [x]</a>
        </div>
        <pre>
        %(script)s
        </pre>
    </div>
    <!--css div popup end-->

    </td>
</tr>
""" # variables: (tid, Class, style, desc, status)


    REPORT_TEST_NO_OUTPUT_TMPL = r"""
<tr id='%(tid)s' class='%(Class)s'>
    <td class='%(style)s'><div class='testcase'>%(desc)s</div></td>
    <td colspan='5' align='center'>%(status)s</td>
</tr>
""" # variables: (tid, Class, style, desc, status)


    REPORT_TEST_OUTPUT_TMPL = r"""
%(id)s: %(output)s
""" # variables: (id, output)



    # ------------------------------------------------------------------------
    # ENDING
    #

    ENDING_TMPL = """<div id='ending'>&nbsp;</div>"""

# -------------------- The end of the Template class -------------------


TestResult = unittest.TestResult

class _TestResult(TestResult):
    # note: _TestResult is a pure representation of results.
    # It lacks the output and reporting ability compares to unittest._TextTestResult.

    def __init__(self, verbosity=1):
        TestResult.__init__(self)
        self.stdout0 = None
        self.stderr0 = None
        self.success_count = 0
        self.failure_count = 0
        self.error_count = 0
        self.verbosity = verbosity

        # result is a list of result in 4 tuple
        # (
        #   result code (0: success; 1: fail; 2: error),
        #   TestCase object,
        #   Test output (byte string),
        #   stack trace,
        # )
        self.result = []


    def startTest(self, test):
        TestResult.startTest(self, test)
        # just one buffer for both stdout and stderr
        self.outputBuffer = io.StringIO()  # self.outputBuffer = StringIO.StringIO()
        stdout_redirector.fp = self.outputBuffer
        stderr_redirector.fp = self.outputBuffer
        self.stdout0 = sys.stdout
        self.stderr0 = sys.stderr
        sys.stdout = stdout_redirector
        sys.stderr = stderr_redirector


    def complete_output(self):
        """
        Disconnect output redirection and return buffer.
        Safe to call multiple times.
        """
        if self.stdout0:
            sys.stdout = self.stdout0
            sys.stderr = self.stderr0
            self.stdout0 = None
            self.stderr0 = None
        return self.outputBuffer.getvalue()


    def stopTest(self, test):
        # Usually one of addSuccess, addError or addFailure would have been called.
        # But there are some path in unittest that would bypass this.
        # We must disconnect stdout in stopTest(), which is guaranteed to be called.
        self.complete_output()


    def addSuccess(self, test):
        self.success_count += 1
        TestResult.addSuccess(self, test)
        output = self.complete_output()
        self.result.append((0, test, output, ''))
        if self.verbosity > 1:
            sys.stderr.write('ok ')
            sys.stderr.write(str(test))
            sys.stderr.write('\n')
        else:
            sys.stderr.write('../venv/Lib')

    def addError(self, test, err):
        self.error_count += 1
        TestResult.addError(self, test, err)
        _, _exc_str = self.errors[-1]
        output = self.complete_output()
        self.result.append((2, test, output, _exc_str))
        if self.verbosity > 1:
            sys.stderr.write('E  ')
            sys.stderr.write(str(test))
            sys.stderr.write('\n')
        else:
            sys.stderr.write('E')

    def addFailure(self, test, err):
        self.failure_count += 1
        TestResult.addFailure(self, test, err)
        _, _exc_str = self.failures[-1]
        output = self.complete_output()
        self.result.append((1, test, output, _exc_str))
        if self.verbosity > 1:
            sys.stderr.write('F  ')
            sys.stderr.write(str(test))
            sys.stderr.write('\n')
        else:
            sys.stderr.write('F')


class HTMLTestRunner(Template_mixin):
    """
    """
    def __init__(self, stream=sys.stdout, verbosity=1, title=None, description=None):
        self.stream = stream
        self.verbosity = verbosity
        if title is None:
            self.title = self.DEFAULT_TITLE
        else:
            self.title = title
        if description is None:
            self.description = self.DEFAULT_DESCRIPTION
        else:
            self.description = description

        self.startTime = datetime.datetime.now()


    def run(self, test):
        "Run the given test case or test suite."
        result = _TestResult(self.verbosity)
        test(result)
        self.stopTime = datetime.datetime.now()
        self.generateReport(test, result)
        print('\nTime Elapsed: %s' % (self.stopTime-self.startTime))  # print >>sys.stderr, '\nTime Elapsed: %s' % (self.stopTime-self.startTime)
        return result


    def sortResult(self, result_list):
        # unittest does not seems to run in any particular order.
        # Here at least we want to group them together by class.
        rmap = {}
        classes = []
        for n,t,o,e in result_list:
            cls = t.__class__
            if not cls in rmap:  # if not rmap.has_key(cls):
                rmap[cls] = []
                classes.append(cls)
            rmap[cls].append((n,t,o,e))
        r = [(cls, rmap[cls]) for cls in classes]
        return r


    def getReportAttributes(self, result):
        """
        Return report attributes as a list of (name, value).
        Override this to add custom attributes.
        """
        startTime = str(self.startTime)[:19]
        duration = str(self.stopTime - self.startTime)
        status = []
        if result.success_count: status.append('Pass %s'    % result.success_count)
        if result.failure_count: status.append('Failure %s' % result.failure_count)
        if result.error_count:   status.append('Error %s'   % result.error_count  )
        if status:
            status = ' '.join(status)
        else:
            status = 'none'
        return [
            ('Start Time', startTime),
            ('Duration', duration),
            ('Status', status),
        ]


    def generateReport(self, test, result):
        report_attrs = self.getReportAttributes(result)
        generator = 'HTMLTestRunner %s' % __version__
        stylesheet = self._generate_stylesheet()
        heading = self._generate_heading(report_attrs)
        report = self._generate_report(result)
        ending = self._generate_ending()
        output = self.HTML_TMPL % dict(
            title = saxutils.escape(self.title),
            generator = generator,
            stylesheet = stylesheet,
            heading = heading,
            report = report,
            ending = ending,
        )
        self.stream.write(output.encode('utf8'))


    def _generate_stylesheet(self):
        return self.STYLESHEET_TMPL


    def _generate_heading(self, report_attrs):
        a_lines = []
        for name, value in report_attrs:
            line = self.HEADING_ATTRIBUTE_TMPL % dict(
                    name = saxutils.escape(name),
                    value = saxutils.escape(value),
                )
            a_lines.append(line)
        heading = self.HEADING_TMPL % dict(
            title = saxutils.escape(self.title),
            parameters = ''.join(a_lines),
            description = saxutils.escape(self.description),
        )
        return heading


    def _generate_report(self, result):
        rows = []
        sortedResult = self.sortResult(result.result)
        for cid, (cls, cls_results) in enumerate(sortedResult):
            # subtotal for a class
            np = nf = ne = 0
            for n,t,o,e in cls_results:
                if n == 0: np += 1
                elif n == 1: nf += 1
                else: ne += 1

            # format class description
            if cls.__module__ == "__main__":
                name = cls.__name__
            else:
                name = "%s.%s" % (cls.__module__, cls.__name__)
            doc = cls.__doc__ and cls.__doc__.split("\n")[0] or ""
            desc = doc and '%s: %s' % (name, doc) or name

            row = self.REPORT_CLASS_TMPL % dict(
                style = ne > 0 and 'errorClass' or nf > 0 and 'failClass' or 'passClass',
                desc = desc,
                count = np+nf+ne,
                Pass = np,
                fail = nf,
                error = ne,
                cid = 'c%s' % (cid+1),
            )
            rows.append(row)

            for tid, (n,t,o,e) in enumerate(cls_results):
                self._generate_report_test(rows, cid, tid, n, t, o, e)

        report = self.REPORT_TMPL % dict(
            test_list = ''.join(rows),
            count = str(result.success_count+result.failure_count+result.error_count),
            Pass = str(result.success_count),
            fail = str(result.failure_count),
            error = str(result.error_count),
        )
        return report


    def _generate_report_test(self, rows, cid, tid, n, t, o, e):
        # e.g. 'pt1.1', 'ft1.1', etc
        has_output = bool(o or e)
        tid = (n == 0 and 'p' or 'f') + 't%s.%s' % (cid+1,tid+1)
        name = t.id().split('.')[-1]
        doc = t.shortDescription() or ""
        desc = doc and ('%s: %s' % (name, doc)) or name
        tmpl = has_output and self.REPORT_TEST_WITH_OUTPUT_TMPL or self.REPORT_TEST_NO_OUTPUT_TMPL

        # o and e should be byte string because they are collected from stdout and stderr?
        if isinstance(o,str):
            # TODO: some problem with 'string_escape': it escape \n and mess up formating
            # uo = unicode(o.encode('string_escape'))
            uo = o  # uo = o.decode('latin-1')
        else:
            uo = o.decode('utf-8')  # uo = o
        if isinstance(e,str):
            # TODO: some problem with 'string_escape': it escape \n and mess up formating
            # ue = unicode(e.encode('string_escape'))
            ue=e  # ue = e.decode('latin-1')
        else:
            ue = e.decode('utf-8')  # ue = e

        script = self.REPORT_TEST_OUTPUT_TMPL % dict(
            id = tid,
            output = saxutils.escape(uo+ue),
        )

        row = tmpl % dict(
            tid = tid,
            Class = (n == 0 and 'hiddenRow' or 'none'),
            style = n == 2 and 'errorCase' or (n == 1 and 'failCase' or 'none'),
            desc = desc,
            script = script,
            status = self.STATUS[n],
        )
        rows.append(row)
        if not has_output:
            return

    def _generate_ending(self):
        return self.ENDING_TMPL


##############################################################################
# Facilities for running tests from the command line
##############################################################################

# Note: Reuse unittest.TestProgram to launch test. In the future we may
# build our own launcher to support more specific command line
# parameters like test title, CSS, etc.
class TestProgram(unittest.TestProgram):
    """
    A variation of the unittest.TestProgram. Please refer to the base
    class for command line parameters.
    """
    def runTests(self):
        # Pick HTMLTestRunner as the default test runner.
        # base class's testRunner parameter is not useful because it means
        # we have to instantiate HTMLTestRunner before we know self.verbosity.
        if self.testRunner is None:
            self.testRunner = HTMLTestRunner(verbosity=self.verbosity)
        unittest.TestProgram.runTests(self)

main = TestProgram

##############################################################################
# Executing this module from the command line
##############################################################################

if __name__ == "__main__":
    main(module=None)
```
### yamlconfig
```python
# -*- ecoding: utf-8 -*-
# @ModuleName: yamlconfig
# @Author: 杨天龙
# @Time: 2022/11/11 21:42
import os
import yaml


class ReadYaml():
    def __init__(self, config_path, param=None):
        self.config_path = config_path  # 配置文件路径
        self.param = param  # 不传默认获取所有数据

    def get_data(self, encoding='utf-8'):
        with open(self.config_path,encoding=encoding) as f:
            config_data = yaml.load(f.read(), Loader=yaml.FullLoader)
            if self.param == None:
                return config_data
            else:
                return config_data.get(self.param)  # 获取键为param的值


if __name__ == '__main__':
    config_path = os.path.abspath(os.path.join(os.getcwd(), '..', 'config/config_file'))
    data = ReadYaml(config_path, 'PACKAGE_NAME').get_data()
    print(data)
```
## README.md
```python
# 说明性文件，告诉团队成员框架需要的环境以及用法 #

小红书ui自动化测试，运行RUN_main.py文件执行所有测试用例
用到了[uiautomator2](http://e.betheme.net/article/show-80644.html?action=onClick)
[uiautomator2笔记](https://blog.csdn.net/baidu_39372836/article/details/122367121)

## Uiautomator2原理介绍
1.uiautomator2 是一个可以使用Python对Android设备进行UI自动化的库。其底层基于Google uiautomator，Google提供的uiautomator库可以获取屏幕上任意一个APP的任意一个控件属性，并对其进行任意操作，目前仅支持android平台的原生应用测试，

## 安装uiautomator2
pip install uiautomator2
pip install pillow  # 截图用
pip install weditor  # 查看元素
pip install unittestreport  # 生成测试报告，发邮件，数据驱动

##  录制视频用到
pip3 install -U "uiautomator2[image]" -i https://pypi.doubanio.com/simple

[comment]: <> (pip install opencv-python)

[comment]: <> (pip install imageio)
 
## 初始化
部署相关的守护进程。
电脑连接上一个手机或多个手机, 确保adb已经添加到环境变量中，执行下面的命令会自动安装本库所需要的设备端程序：uiautomator-server 、atx-agent、openstf/minicap、openstf/minitouch
python -m uiautomator2 init
安装完成，设备上会多一个uiautomator的应用。
配置手机设备参数：
有两种方法，一种是通过WIFI，另一种是通过USB数据线将手机链接电脑。
WiFi连接更方便一点，需要保持PC和手机使用的一个WIFI，查看手机连接WIFI的IP地址。

## 测试
import uiautomator2 as u2
d = u2.connect()
print(d.info)

## adb命令logcat抓取日志
[链接地址](https://blog.csdn.net/m0_64776928/article/details/126005119)
adb shell ps -A | grep logcat  # 查看log进程(在windows下将grep换成findstr)
adb shell kill 进程号  # 杀死进程

## 二者的区别是：spa
使用os.popen来获取设备号，使用os.system来启动macaca服务
（1）os.system(cmd)的返回值只会有0(成功),1,2c
（2）os.popen(cmd)会把执行的cmd的输出做为值返回。
popen
此方法能够使用read、readline、readlines方法进行内容操做。
```