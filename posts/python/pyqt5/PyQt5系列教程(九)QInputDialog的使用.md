### 软硬件环境
* Ubuntu 15.10 32bit
* Python 3.5.1
* PyQt 5.5.1

### 前言
输入框是界面开发中非常常见的控件，本文就来看看PyQt5中QInputDialog的使用

### 实例
本例将实现如下效果图：

![QInputDialog_01.png](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/mac/PyQt5/QInputDialog_01.png)

由用户输入生产订单号，当订单号不空且点击OK按钮时，则跳转到其他界面，否则，弹框报错，退出系统。


	def showPONInputDialog(self):
        opN,okPressed = QInputDialog.getText(self,"**科技","请输入生产订单号:",QLineEdit.Normal, " ")
        if okPressed and opN.strip():
            print('PON:' + opN)
            logging.info('PON:' + opN)
            self.poNumber = opN
            self.lineedit_order.setText(self.poNumber)

        else:
            QMessageBox.critical(self,ERRORTITLE,"请输入订单号,点击OK进入系统!")
            exit(1)

getText方法的返回值是一个元组(str,bool)，样式为QLineEdit.Normal，常用的还有QLineEdit.Password。方法showPONInputDialog一般放再mainwindown中实现。
