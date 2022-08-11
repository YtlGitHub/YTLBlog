---
title: python App
date: 2022-05-10 18:42:13
tags: 自动化测试
categories: 自动化测试
---

python编写app自动化脚本

## 安装环境
python 3.8
uiautomator2
unittestreport

## 使用文档
unittestreport 官方使用文档：https://unittestreport.readthedocs.io/en/latest/
unittestreport资料网址：https://www.cnblogs.com/nmb-musen/p/13596241.html
断言方法：https://www.cnblogs.com/syw20170419/p/11015962.html
fiddler抓包设置：https://www.cnblogs.com/nmb-musen/p/10621430.html

## 开始编写代码
```py
# coding: utf-8
#
import unittest
import os
import time
import uiautomator2 as u2
from unittestreport import TestRunner, rerun


class MyTestCase(unittest.TestCase):
  @classmethod
  def setUpClass(self):
    print("整个测试用例的前置条件")
    self.devicesSerial = os.popen('adb devices').read().split()[-2]  # 获取设备序列号
    print(self.devicesSerial)
    self.d = u2.connect(self.devicesSerial)  # 连接序列号
    self.d.implicitly_wait(10)

  @classmethod
  def tearDownClass(self):
    print("整个测试用例的后置条件")

  def setUp(self):
    print("每条用例的前置条件")
    self.d.app_stop_all()

  def tearDown(self):
    print("每条用例的后置条件")

  # @rerun(count=2, interval=2)  # 装饰器测试用例运行失败重运行4，每次间隔2秒
  def test_something(self):
    """测试软件商店"""
    self.d(text="软件商店").click()
    self.d(text="我的").click()
    a = self.d(text="首页").exists(timeout=5)
    print(a)
    self.assertIsNotNone(a)


    try:
      # assert expected in result or result in expected
      # self.assertEqual(b, a)
      self.assertTrue(a)
    except Exception as e:
      now = time.time()  # 现在的时间
      now_time = time.strftime("%Y%m%d_%H%M%S", time.localtime(now))  # 当前时间
      error_screenshot_name = f'错误截图{now_time}.png'
      self.d.screenshot(f"../images/{error_screenshot_name}")  # 错误截图保存路径
      msg = f'<img src="../images/{error_screenshot_name}" width="400" height="800" title="{error_screenshot_name}" alt="错误截图"/>'
      print(msg)
      raise e
    else:
      print("执行成功")


if __name__ == '__main__':
  #  unittest.main()

  suite = unittest.TestSuite()  # 实例化一个测试套件
  suite.addTest(unittest.makeSuite(MyTestCase))  # 加载MyTestCase里面所有带test*的测试用例
  now = time.time()  # 现在的时间
  now_time = time.strftime("%Y%m%d_%H%M%S", time.localtime(now))  # 当前时间
  report_name = f"测试报告{now_time}.html"  # 报告名后面加当前时间
  report_path = os.path.join(os.path.abspath(os.path.dirname(os.getcwd())), "report")
  print(report_path)
  runner = TestRunner(suite,
                      filename=report_name,
                      report_dir=report_path,
                      title="报告名",
                      tester="ytl",
                      desc="ytl执行的测试报告",
                      templates=1
                      )
  # runner.run()
  runner.rerun_run(count=0, interval=2)  # count：用来指定用例失败重运行的次数 interval：指定每次重运行的时间间隔

  # 邮件发送测试报告
  '''
  runner.send_email(host="smtp.qq.com",  # smtp-mail.outlook.com
                  port=465,  # 587
                  user="1766842398@qq.com",
                  password="robtftuxckfvdcci",
                  to_addrs=["tlyang@qq.com", "1766842398@qq.com"])
  '''

  """
  参数介绍
  host： smtp服务器地址
  port：端口
  user：邮箱账号
  password：smtp服务授权码
  to_addrs：收件人邮箱地址（一个收件人传字符串，多个收件人传列表）
  """

```
