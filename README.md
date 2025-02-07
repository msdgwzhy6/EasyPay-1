# EasyPay(易支付)---- 一个便捷易用的Android平台聚合支付框架

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/mit-license.php)

[项目GitHub链接](https://github.com/kingofglory/EasyPay)

[TOC]

## Vision (愿景)
### 成为Android平台最便捷易用的支付框架

------

## Introduction（介绍）
This is a framework for Android Developers easily to use Alipay,WechatPay and UnionPay in Android project.

EasyPay(易支付)集成并高度封装了Android平台的微信支付，支付宝支付以银联支付。使用此库，开发者可以使用简单轻松方便的api实现支付功能，大量节省集成配置时间。

------

## Screenshot（支付效果图）

![screenshot.gif](https://github.com/kingofglory/EasyPay/blob/master/screensshot/screenshot.gif)

------

## Usage（使用）
使用步骤非常简单，总共两步：1.集成依赖库；2.相关支付Api调用。

### 使用步骤一、 集成依赖库
集成方式有以下两种，根据需要选择其中一种集成即可：

**远程依赖库集成方式**  Or **下载源码作为Module导入集成方式**；


#### 远程依赖库集成方式

在Project中主App模块中的build.gradle的dependencies块中添加以下依赖：

#### 1) EasyPay支付基类库（必选）:
> 注意：本步骤必须添加，因为该库是EasyPay基类库

	implementation 'com.xgr.easypay:EasyPay:2.0.0'

#### 2) 根据需要集成微信支付、支付宝支付、银联支付
> 注意：以下三个库可根据实际需要增删

##### 1）微信支付集成（可选）：

    implementation 'com.xgr.easypay:wechatpay:2.0.0'

##### 2）支付宝支付集成（可选）：

    implementation 'com.xgr.easypay:alipay:2.0.0'

##### 3）银联支付集成（可选）：

    implementation 'com.xgr.easypay:unionpay:2.0.0'

远程依赖集成方式到此结束。

#### 下载源码后作为module导入：

#### 1) 集成基类依赖库（必选）：
```java
implementation project(':easypay')
```

#### 2) 根据需要集成其他支付依赖库

##### 1）微信支付集成（可选）：

```java
implementation project(':wechatpay')
```
##### 2）支付宝集成（可选）：

```java
implementation project(':alipay')
```
##### 3）银联支付集成（可选）：

```java
implementation project(':unionpay')
```
下载源码作为Module导入集成方式到此结束。

------


### 使用步骤二、相关支付Api调用

#### 微信支付（共两步）
##### 1）配置
在项目主App模块的build.gradle文件的android{}块->defaultConfig{}块中配置applicationId,具体如下：
```java
        manifestPlaceholders = [
                APPLICATION_ID: applicationId,
        ]
```
##### 2）api调用
``` java
    private void wxpay(){
        //实例化微信支付策略
        WXPay wxPay = WXPay.getInstance();
        //构造微信订单实体。一般都是由服务端直接返回。
        WXPayInfoImpli wxPayInfoImpli = new WXPayInfoImpli();
        wxPayInfoImpli.setTimestamp("");
        wxPayInfoImpli.setSign("");
        wxPayInfoImpli.setPrepayId("");
        wxPayInfoImpli.setPartnerid("");
        wxPayInfoImpli.setAppid("");
        wxPayInfoImpli.setNonceStr("");
        wxPayInfoImpli.setPackageValue("");
        //策略场景类调起支付方法开始支付，以及接收回调。
        EasyPay.pay(wxPay, this, wxPayInfoImpli, new IPayCallback() {
            @Override
            public void success() {
                toast("支付成功");
            }

            @Override
            public void failed(int code, String msg) {
                toast("支付失败");
            }

            @Override
            public void cancel() {
                toast("支付取消");
            }
        });
    }
```
微信支付到此结束

#### 支付宝支付（共一步）
``` java
    private void alipay(){
        //实例化支付宝支付策略
        AliPay aliPay = new AliPay();
        //构造支付宝订单实体。一般都是由服务端直接返回。
        AlipayInfoImpli alipayInfoImpli = new AlipayInfoImpli();
        alipayInfoImpli.setOrderInfo("");
        //策略场景类调起支付方法开始支付，以及接收回调。
        EasyPay.pay(aliPay, this, alipayInfoImpli, new IPayCallback() {
            @Override
            public void success() {
                toast("支付成功");
            }

            @Override
            public void failed(int code, String msg) {
                toast("支付失败");
            }

            @Override
            public void cancel() {
                toast("支付取消");
            }
        });
    }
```
支付宝支付到此结束

#### 银联支付（共一步）
``` java
    private void unionpay(){
        //实例化银联支付策略
        UnionPay unionPay = new UnionPay();
        //构造银联订单实体。一般都是由服务端直接返回。测试时可以用Mode.TEST,发布时用Mode.RELEASE。
        UnionPayInfoImpli unionPayInfoImpli = new UnionPayInfoImpli();
        unionPayInfoImpli.setTn("814144587819703061900");
        unionPayInfoImpli.setMode(Mode.TEST);
        //策略场景类调起支付方法开始支付，以及接收回调。
        EasyPay.pay(unionPay, this, unionPayInfoImpli, new IPayCallback() {
            @Override
            public void success() {
                toast("支付成功");
            }

            @Override
            public void failed(int code, String msg) {
                toast("支付失败");
            }

            @Override
            public void cancel() {
                toast("支付取消");
            }
        });
    }
```

银联支付到此结束

------

## 项目实现介绍
易支付编码实现遵循设计模式六大原则，并且使用了单例以及策略模式来实现整个库,扩展性良好,可以轻松扩展其他支付方式。

``` java
├── activity
│   ├── UnionPayAssistActivity.java     //银联辅助Activity，负责调起银联支付接口以及接收回调。客户端无需关心。
│   └── WXPayEntryBaseActivity.java     //微信支付回调Activity封装。客户端需继承该Activity并实现getAppId()方法。
├── alipay
│   ├── AliPay.java                     //支付宝支付api封装，实现了IPayStrategy接口
│   ├── AlipayInfoImpli.java
│   └── AliPayResult.java
│   └── ResultCode.java
├── base
│   ├── IPayInfo.java                   //易支付支付信息基类接口
│   └── IPayStrategy.java               //易支付支付策略基类接口
├── callback
│   └── IPayCallback.java               //易支付统一回调接口
├── EasyPay.java                        //易支付场景类，客户端调用者
├── unionpay
│   ├── Mode.java
│   ├── UnionPay.java                   //银联支付api封装，，实现了IPayStrategy接口
│   └── UnionPayInfoImpli.java
│   └── UnionPayErrCode.java
└── wxpay
      ├── WXPay.java                      //微信支付api封装，实现了IPayStrategy接口
      └── WXPayInfoImpli.java
      └── WXErrCodeEx.java

```
------

## 框架扩展新的支付平台（如美团、京东等其他支付）
EasyPay从立项之初，就充分考虑了代码扩展性，启用策略模式，全部采用面向接口编程，遵循依赖倒置设计原则。从支付基类扩展出新的支付非常容易。仅需三步。下面给出参考步骤。更具体请参照项目中支付宝或者微信或者银联支付方式封装。
### 1) 支付订单信息类实现IPayInfo接口
```java
public class XXpayInfoImpli implements IPayInfo {
    public xxType xxField = xxx;
    public yyTYpe xxFiled = yyy;
    ...other Field
}
```
### 2) 支付策略类实现IPayStrategy。
将第一步中支付实体类传入泛型。支付策略的初衷是将某种支付所有操作都进行集中封装，凡是业务需要用到该支付的地方，都调用这个类即可。
```java
public class XXPay implements IPayStrategy<XXpayInfoImpli> {
    private AlipayInfoImpli alipayInfoImpli;
    private static IPayCallback sPayCallback;

    @Override
    public void pay(Activity activity, AlipayInfoImpli payInfo, IPayCallback payCallback) {
        this.mActivity = activity;
        this.alipayInfoImpli = payInfo;
        sPayCallback = payCallback;
    }

    ...other method
}
```
完成上述两步后，根据业务在需要地方调用即可，需要注意是当某支付平台支付回调比较分散时，可在对应地方将调用转发给上述支付类即可。这样，可以将逻辑集中到一个类处理。如不理解这段话，可以看银联支付UnionPayAssistActivity中的onActivityResult()方法，就将逻辑转给 UnionPay.handleResult(this,data)处理了。

### 3）调用Api
```java
        //实例化支付策略
        XXpay xxPay = new XXPay();
        //构造支付宝订单实体。一般都是由服务端直接返回。
        XXpayInfoImpli xxpayInfoImpli = new XXpayInfoImpli();
        xxpayInfoImpli.setXXFiled();
        ...
        //策略场景类调起支付方法开始支付，以及接收回调。
        EasyPay.pay(xxPay, this, xxpayInfoImpli, new IPayCallback() {
            @Override
            public void success() {
                toast("支付成功");
            }

            @Override
            public void failed(int code, String message) {
                toast("支付失败");
            }

            @Override
            public void cancel() {
                toast("支付取消");
            }
        });
```
扩展介绍到此结束

------

## (ChangeLog) 更新日志
#### v2.0.0更新(2019/10/27)
1. 极限地精简微信支付集成和使用步骤，并更新微信支付SDK
2. 更新支付宝SDK
3. 更新银联SDK
4. 支付回调fail方法返回code和message

------

## 联系我

#### 1) 有问题提[Issues](https://github.com/kingofglory/EasyPay/issues)。欢迎大家交流想法。

#### 2) 邮箱联系(Email : kingofglory@yeah.net)

#### 3) 付费加QQ群
关于为什么，说几点：
1. 维护群和解答问题将耗费作者额外时间和精力；
2. 付费将迫使同学们为了不付费而更加认真去思考问题和查看源码；
3. 有同学觉得用了我的框架，节省了时间，提高了效率的，愿意请我喝杯咖啡交个朋友的，非常欢迎；
4. 有些同学可能对本框架不熟悉或者碰到了其他Android支付问题，需要快速咨询的，可以考虑付费入群，不保证能解决，但会尽全力而为。

感谢大家，希望一起起步。

加群步骤：扫码支付时备注：easypay+你的qq号。我看到后将拉你入群。付款不退，慎重考虑。

<figure class="half">
    <img src="https://github.com/kingofglory/EasyPay/blob/master/screensshot/wechatpay.jpg">
    <img src="https://github.com/kingofglory/EasyPay/blob/master/screensshot/alipay.jpg">
</figure>

------

## License

	MIT License

	
	Copyright (c) 2017 kingofglory
	

	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:

	
	The above copyright notice and this permission notice shall be included in all
	copies or substantial portions of the Software.

	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
	SOFTWARE.
