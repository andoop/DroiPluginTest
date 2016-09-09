## DroidPluginTest
###[360DroidPlugin](https://github.com/Qihoo360/DroidPlugin)使用demo
###github:https://github.com/andoop/DroiPluginTest

####DroidPlugin介绍
>它是360手机助手团队在Android系统上实现了一种插件机制。它可以在无需安装、修改的情况下运行APK文件,此机制对改进大型APP的架构，实现多团队协作开发具有一定的好处。

#####优点
>1. 支持Androd 2.3以上系统 
2. 插件APK完全不需做任何修改，可以独立安装运行、也可以做插件运行。要以插件模式运行某个APK，你无需重新编译、无需知道其源码。 
3. 插件的四大组件完全不需要在Host程序中注册，支持Service、Activity、BroadcastReceiver、ContentProvider四大组件 
4. 插件之间、Host程序与插件之间会互相认为对方已经”安装”在系统上了。 
5. API低侵入性：极少的API。HOST程序只是需要一行代码即可集成Droid Plugin 
6. 超强隔离：插件之间、插件与Host之间完全的代码级别的隔离：不能互相调用对方的代码。通讯只能使用Android系统级别的通讯方法。 
7. 支持所有系统API 
8. 资源完全隔离：插件之间、与Host之间实现了资源完全隔离，不会出现资源窜用的情况。 
9. 实现了进程管理，插件的空进程会被及时回收，占用内存低。 
10. 插件的静态广播会被当作动态处理，如果插件没有运行（即没有插件进程运行），其静态广播也永远不会被触发。

#####不足
>1. 加载时界面存在明显的等待。(参见360市场管理界面中的”手机清理”功能) 
2. 无法在插件中发送具有自定义资源的Notification，例如： a. 带自定义RemoteLayout的Notification b. 图标通过R.drawable.XXX指定的通知（插件系统会自动将其转化为Bitmap） 
3. 无法在插件中注册一些具有特殊Intent Filter的Service、Activity、BroadcastReceiver、ContentProvider等组件以供Android系统、已经安装的其他APP调用。 
4. 缺乏对Native层的Hook，对某些带native代码的apk支持不好，可能无法运行。比如一部分游戏无法当作插件运行。

####demo截图
<img src="http://i.imgur.com/mIBCM75.png" width=200 height=330/>
<img src="http://i.imgur.com/pHt613z.png" width=200 height=330/>
>加载的应用都没有安装，使用DroidPlugin可以直接调用

####demo讲解

#####关联DroidPlugin的library，本demo中的DroidPluginlib
#####自定义Application，并使用

	public class MApplication extends Application{
	    @Override
	    public void onCreate() {
	        super.onCreate();
	        //这里必须在super.onCreate方法之后，顺序不能变
	        PluginHelper.getInstance().applicationOnCreate(getBaseContext());
	    }
	
	    @Override
	    protected void attachBaseContext(Context base) {
	        //调用相关方法
	        PluginHelper.getInstance().applicationAttachBaseContext(base);
	        super.attachBaseContext(base);
	    }
	}

>在自己的application中调用PluginHelper的applicationOnCreate和applicationAttachBaseContext方法

#####修改DroidPlugin

    将插件中DroidPluginlib\AndroidManifest.xml
	中所有的provider对应的authorities修改成自己的，
	默认为com.morgoo.droidplugin_stub_P00
    并且修改PluginManager.java中的PluginManager.STUB_AUTHORITY_NAME为你的值

#####安装插件

 	PluginManager.getInstance().installPackage(apkfile, 0);
>apkfile就是要加载的apk文件的路径，本例中，apk文件放到了/sdcard/360Download，如/sdcard/360Download/xxx.apk

#####启动插件
	 PackageManager pm = getActivity().getPackageManager();
	 Intent intent = pm.getLaunchIntentForPackage(packageName);
	 intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	 startActivity(intent);
>开启插件，就跟打开一个已经安装的应用一样，packageName就是要启动的插件的包名

####卸载插件

	PluginManager.getInstance().deletePackage(packageName, 0);
>packageName为要卸载的插件的包名

详情请看demo，主要方法就这些

###欢迎关注andoop ，周一、二更新内容，干货永不断！
