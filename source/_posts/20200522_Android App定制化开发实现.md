---
title: Android App定制化开发实现
date: 2020-05-22 19:49:11
---

#### 方法一，通过gradle构建生成BuildConfig.java静态变量
```java
// 在build.gradle里配置defaultConfig
defaultConfig {
    buildConfigField "boolean", "IS_DEBUG", "true"
}
// 或
buildTypes {
    debug {
        buildConfigField "boolean", "IS_DEBUG", "true"
    }

    releas {
        buildConfigField "boolean", "IS_DEBUG", "true"
    }
}

// 通过BuildConfig.isDebug拿到值，如果是组件化则通过反射拿到BuildConfig.java里的值
public static Object getBuildConfigValue(Context context, String key) {
    try {
        Class<?> clazz = Class.forName(context.getPackageName() + ".BuildConfig");
        Field field = clazz.getField(key);
        return field.get(clazz);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}

------------------

// BuildConfig.java文件如下
public final class BuildConfig {
    public static final boolean IS_DEBUG = true;
}
```

#### 方法二，通过自定义config.properties文件读取
```java
// 在app moudle的res平级目录新建assets文件夹，放入config.properties文件，如下
#true 是debug模式，else 不是
IS_DEBUG=true

// 在BaseApplication拿到properties的值
public static boolean isDebug;
private void loadConfig() {
    InputStream in = null;
    property = new Properties();
    try {
        in = getResources().getAssets().open("config.properties");
        property.load(in);
        isDebug = Boolean.parseBoolean(property.getProperty("IS_DEBUG"));
    } catch (IOException e) {
        LogUtils.e("load properties error" + e);
    } finally {
        if (in != null) {
            try {
                in.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```
