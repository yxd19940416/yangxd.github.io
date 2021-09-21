---
title: pod是什么
top: false
cover: false
toc: true
mathjax: true
date: 2021-08-22 20:07:40
password:
summary:
tags: 
    - cocoapods
    - ios
categories: ios
---

### 什么是Podfile
CocoaPods是用ruby实现的，因此Podfile文件的语法就是ruby的语法。
podfile是一个说明文件，用以描述管理一个或者多个Xcode project的target的依赖库。这个文件应该且必须被命名为Podfile。
Podfile可以非常简单,下面的例子增加了Alamofire依赖库到单个target:

```
target 'MyApp' do 
    use_frameworks! 
    pod 'Alamofire', '~> 3.0'
end
```
下面是一个更复杂的例子，Podfile链接了app和它的测试bundle: 
```
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/Artsy/Specs.git'

platform :ios, '9.0'
inhibit_all_warnings!

target 'MyApp' do
    pod 'GoogleAnalytics', '~> 3.1'
    # Has its own copy of OCMock 
    # and has access to GoogleAnalytics via the app 
    # that hosts the test target

    target 'MyAppTests' do 
        inherit! :search_paths 
        pod 'OCMock', '~> 2.0.1' 
    end
end

post_install do |installer|
    installer.pods_project.targets.each do |target| 
        puts target.name 
    end
end
```
如果你希望多个target共享同一个pods，那么可以用关键字abstract_target：
```
# There are no targets called "Shows" in any Xcode projects
abstract_target 'Shows' do 
    pod 'ShowsKit' 
    pod 'Fabric'

    # Has its own copy of ShowsKit + ShowWebAuth
    target 'ShowsiOS' do 
        pod 'ShowWebAuth' 
    end

    # Has its own copy of ShowsKit + ShowTVAuth 
    target 'ShowsTV' do 
        pod 'ShowTVAuth' 
    end
end
```
Podfile中自带一个隐藏的、默认的abstract target，所以你也可以用如下的方式达到上面例子的同样效果：
```
pod 'ShowsKit'
pod 'Fabric'

# Has its own copy of ShowsKit + ShowWebAuth
target 'ShowsiOS' do 
    pod 'ShowWebAuth'
end

# Has its own copy of ShowsKit + ShowTVAuth
target 'ShowsTV' do 
    pod 'ShowTVAuth'
end
```
### 指定pod版本
当开始一个项目,你可能会想要使用最新版本的pod依赖库。 如果是这种情况,只需忽略版本要求。 
`pod 'SSZipArchive'`    
稍后在项目您可能想要使用特定版本的pod依赖库,在这种情况下,您可以指定版本号
`pod 'Objection', '0.9'`
除了没有版本,或特定的一个,也可以使用逻辑运算符: 

'> 0.1' 高于0.1的任何版本
'>= 0.1' 版本0.1或更高版本
'< 0.1' 低于0.1的任何版本
'<= 0.1' 版本0.1或更低的版本
除了逻辑运算符，还有一种运算符：

'~> 0.1.2' 版本0.1.2和0.2版本之间的任意版本,不包括0.2和比0.2更高的版本
'~> 0.1' 版本0.1和版本1.0之间的任意版本,不包括1.0和比1.0更高的版本
'~> 0' 版本0或比版本0更高的版本,这基本上和不指定版本号的效果是一样的。
