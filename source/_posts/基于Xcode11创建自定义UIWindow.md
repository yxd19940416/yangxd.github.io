---
title: 基于Xcode11创建自定义UIWindow
date: 2021-10-21 20:07:22
top: false
cover: false
password:
toc: true
mathjax: true
summary: 基于Xcode11创建自定义UIWindow
tags: 
    - ios
    - objective-c
categories: ios
---
# 基于Xcode11创建自定义UIWindow

最近更新Xcode 11.4，在创建项目之后发现多了苹果分屏技术，新增了SceneDelegate这个文件，另外AppDelegate文件结构也发生了变化，给人一种似曾相识又不同的感觉，总的来说之前熟悉的Window不再由AppDelegate管理，而是交给了SceneDelegate。

如下图即可看出目录结构和info配置变化：
![](16277872068447.png)

简要介绍Application Scene Manifest分屏配置：
```
enable Multipe Windows --- 是否允许分屏
Scene Configuratiton --- 屏幕配置项
Application Session Role --- 程序屏幕配置规则（为每个Scene指定规则）
Configuration Name --- 配置名称
Delegate Class Name --- 代理类名称
Storyboard Name --- Storyboard名称
```
解读如下：

创建项目工程时，系统默认为我们创建了一个名为Default Configuratiton 的默认配置，代理类名称为SceneDelegate,入口名为Main的Storyboard，代码如下：
```
- (UISceneConfiguration *)application:(UIApplication *)application configurationForConnectingSceneSession:(UISceneSession *)connectingSceneSession options:(UISceneConnectionOptions *)options {
    // Called when a new scene session is being created.
    // Use this method to select a configuration to create the new scene with.
    return [[UISceneConfiguration alloc] initWithName:@"Default Configuration" sessionRole:connectingSceneSession.role];
}
```
回到主题，针对这种情况，如果创建我们熟悉的自定义Window呢？

一、针对iOS13系统及以上：保留SceneDelegate，需要修改SceneDelegate里面的代码即可；
```
- (void)scene:(UIScene *)scene willConnectToSession:(UISceneSession *)session options:(UISceneConnectionOptions *)connectionOptions {
    // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
    // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
    // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).
    
    
    if (@available(ios 13, *)) {
        if (scene) {
            self.window = [[UIWindow alloc] initWithWindowScene:(UIWindowScene *)scene];
            self.window.frame = CGRectMake(0, 0, [UIScreen mainScreen].bounds.size.width, [UIScreen mainScreen].bounds.size.height);
            UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:[[ViewController alloc]init]];
            self.window.rootViewController = nav;
            [self.window makeKeyAndVisible];
        }
    }
}
```
效果图：

![-w308](16277872853169.png)


二、针对iOS13系统以下：

a. 删除info.plist文件中的Application Scene Manifest选项;

b. 删除SceneDelegate文件;

c. 删除AppDelegate里面的UISceneSession lifecycle方法;

d. AppDelegate头文件添加window属性;

` @property (strong, nonatomic) UIWindow *window; ` 
e. 修改AppDelegate启动方法：

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    UIViewController *vc = [[UIViewController alloc]init];
    vc.view.backgroundColor = [UIColor greenColor];
    UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:vc];
    self.window.rootViewController = nav;
    [self.window makeKeyAndVisible];
    return YES;
}

```
效果图：

![-w308](16277873129770.png)


至此，我们又回到了曾经熟悉的开发场景。