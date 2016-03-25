---
layout: post
title: AOP框架Dexposed使用详解
date: 2015-08-26
categories: blog
author: guolf
tags: [android, aop,dexposed]
description: Dexposed是基于久负盛名的开源Xposed框架实现的一个Android平台上功能强大的无侵入式运行时AOP框架，项目地址：https://github.com/alibaba/dexposed

---
## 典型的使用场景

* AOP编程
* 插桩（例如测试，性能监控等）
* 在线热更新，修复严重的，紧急的或者安全性的bug
* SDK hooking以提供更好的开发体验

## 如何集成

集成方式很简单，直接dexposed aar添加到您的项目作为编译库，它包含了从“dexposed”目录中的jar文件“dexposedbridge.jar”和两个so文件“libdexposed.so libdexposed_l.so”。Gradle依赖如下所示：

	dependencies {
		compile 'com.taobao.android:dexposed:0.1.1@aar'
	}

在应用启动时进行初始化

	public class MyApplication extends Application {

		private boolean mIsSupported = false; // 设备是否支持dexposed
	    private boolean mIsLDevice = false;  // 设备Android系统是否是Android 5.0及以上

        @Override public void onCreate() {    
         super.onCreate();    
            // Check whether current device is supported (also initialize Dexposed framework if not yet)
            mIsSupported = DexposedBridge.canDexposed(this);
        	mIsLDevice = Build.VERSION.SDK_INT >= 21;
        }
        public boolean isSupported() {
        return mIsSupported;
    	}

	    public boolean isLDevice() {
	        return mIsLDevice;
	    }
    }

## 基本用法

对于某个函数而言，有三个注入点可供选择：函数执行前注入（before），函数执行后注入（after），替换函数执行的代码段（replace）。
例子1：应用中所有的Activity.onCreate(Bundle)函数调用之前和之后增加一些相同的处理：

	// Target class, method with parameter types, followed by the hook callback (XC_MethodHook).
    DexposedBridge.findAndHookMethod(Activity.class, "onCreate", Bundle.class, new XC_MethodHook() {

        // To be invoked before Activity.onCreate().
        @Override protected void beforeHookedMethod(MethodHookParam param) throws Throwable {
                // "thisObject" keeps the reference to the instance of target class.
                Activity instance = (Activity) param.thisObject;

                // The array args include all the parameters.
                Bundle bundle = (Bundle) param.args[0];
                Intent intent = new Intent();
                // XposedHelpers provide useful utility methods.
                XposedHelpers.setObjectField(param.thisObject, "mIntent", intent);

                // Calling setResult() will bypass the original method body use the result as method return value directly.
                if (bundle.containsKey("return"))
                    param.setResult(null);
            }

            // To be invoked after Activity.onCreate()
            @Override protected void afterHookedMethod(MethodHookParam param) throws Throwable {
                XposedHelpers.callMethod(param.thisObject, "sampleMethod", 2);
        }
    });

例子2：在线热更新
我们假设宿主apk的MainActivity.showDialog函数出现问题，需要打补丁，宿主代码如下所示：（类完整路径是:

	package com.taobao.dexposed;

	import android.app.Activity;
	import android.app.AlertDialog;
	import android.content.DialogInterface;
	import android.os.Bundle;
	import android.util.Log;
	import android.view.View;
	import android.widget.TextView;

	import com.taobao.android.dexposed.XC_MethodHook;
	import com.taobao.android.dexposed.DexposedBridge;
	import com.taobao.patch.PatchMain;
	import com.taobao.patch.PatchResult;

	import java.io.File;


	public class MainActivity extends Activity {

		private boolean isSupport = false;

	    private boolean isLDevice= false;
		
		private TextView mLogContent;
		
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			mLogContent = (TextView) (this.findViewById(R.id.log_content));
			// check device if support and auto load libs
	        isSupport = DexposedBridge.canDexposed(this);
	        isLDevice = android.os.Build.VERSION.SDK_INT == 21;
		}

		//Hook system log click
		public void hookSystemLog(View view) {
			if (isSupport) {
				DexposedBridge.findAndHookMethod(isLDevice ? this.getClass(): Log.class, isLDevice ? "showLog" : "d", String.class, String.class, new XC_MethodHook() {
					@Override
					protected void afterHookedMethod(MethodHookParam arg0) throws Throwable {
						String tag = (String) arg0.args[0];
						String msg = (String) arg0.args[1];
						mLogContent.setText(tag + "," + msg);
					}
				});
	            if (isLDevice) {
	                showLog("dexposed", "It doesn't support AOP to system method on ART devices");
	            } else {
	                Log.d("dexposed", "Logs are redirected to display here");
	            }
			} else {
				mLogContent.setText("This device doesn't support dexposed!");
			}
		}

	    private void showLog(String tag, String msg) {
	        Log.d(tag, msg);
	    }
		
		// Hook choreographer click
		public void hookChoreographer(View view) {
			Log.d("dexposed", "hookChoreographer button clicked.");
			if (isSupport && !isLDevice) {
				ChoreographerHook.instance().start();
			} else {
				showLog("dexposed", "This device doesn't support this!");
			}
		}
		
		// Run patch apk
		public void runPatchApk(View view) {
			Log.d("dexposed", "runPatchApk button clicked.");
	        if (isLDevice) {
	            showLog("dexposed", "It doesn't support this function on L device.");
	            return;
	        }
	        if (!isSupport) {
				Log.d("dexposed", "This device doesn't support dexposed!");
				return;
			}
			File cacheDir = getExternalCacheDir();
	    	if(cacheDir != null){
	    		String fullpath = cacheDir.getAbsolutePath() + File.separator + "patch.apk";
	    		PatchResult result = PatchMain.load(this, fullpath, null);
	    		if (result.isSuccess()) {
	    			Log.e("Hotpatch", "patch success!");
	    		} else {
	    			Log.e("Hotpatch", "patch error is " + result.getErrorInfo());
	    		}
	    	}
	    	showDialog();
		}
		
		private void showDialog() {
			AlertDialog.Builder builder = new AlertDialog.Builder(this);
			builder.setTitle("Dexposed sample")
					.setMessage(
							"Please clone patchsample project to generate apk, and copy it to \"/Android/data/com.taobao.dexposed/cache/patch.apk\"")
					.setPositiveButton("ok", new DialogInterface.OnClickListener() {
						public void onClick(DialogInterface dialog, int whichButton) {
						}
					}).create().show();
		}
	}

补丁apk只有一个名为DialogPatch的类，实现了patchloader函数库中的IPatch接口，IPatch接口代码如下所示：

	package com.taobao.patch;

	import android.app.Activity;
	import android.app.AlertDialog;
	import android.content.DialogInterface;

	import com.taobao.android.dexposed.XC_MethodReplacement;
	import com.taobao.android.dexposed.DexposedBridge;

	public class DialogPatch implements IPatch {

		@Override
		public void handlePatch(final PatchParam arg0) throws Throwable {    	
	    	Class<?> cls = null;
			try {
				cls= arg0.context.getClassLoader()
						.loadClass("com.taobao.dexposed.MainActivity");
			} catch (ClassNotFoundException e) {
				e.printStackTrace();
				return;
			}     	
	     	DexposedBridge.findAndHookMethod(cls, "showDialog",
					new XC_MethodReplacement() {
				@Override
				protected Object replaceHookedMethod(MethodHookParam param) throws Throwable {
					Activity mainActivity = (Activity) param.thisObject;
					AlertDialog.Builder builder = new AlertDialog.Builder(mainActivity);
					builder.setTitle("Dexposed sample")
							.setMessage("The dialog is shown from patch apk!")
							.setPositiveButton("ok", new DialogInterface.OnClickListener() {
								public void onClick(DialogInterface dialog, int whichButton) {
								}
							}).create().show();
					return null;                 
				}
			});
		} 
	}

将补丁打包成apk,通过adb push到/Android/data/com.taobao.dexposed/cache/patch.apk,点击runPatchApk即可看到弹出的内容为补丁的内容。

	adb push patch.apk /sdcard/Android/data/com.taobao.dexposed/cache/patch.apk

## 支持的版本

Dexposed支持从Android2.3到4.4（除了3.0）的所有dalvid运行时arm架构的设备，稳定性已经经过实践检验。

| Runtime |  Android Version | Support |
| ------ |:-------------:| -----:|
| Dalvik | 2.2 | Not Test |
| Dalvik | 2.3 | Yes |
| Dalvik | 3.0 | No
| Dalvik | 4.0-4.4 | Yes
| ART | 5.0 | Testing
| ART | 5.1 | No
|  ART | M | No
