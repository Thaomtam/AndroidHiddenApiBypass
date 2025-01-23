# AndroidHiddenApiBypass

[![Android CI status](https://github.com/LSPosed/AndroidHiddenApiBypass/actions/workflows/android.yml/badge.svg?branch=main)](https://github.com/LSPosed/AndroidHiddenApiBypass/actions/workflows/android.yml)

Bypass restrictions on non-SDK interfaces.

## Why AndroidHiddenApiBypass?

- Pure Java: no native code used.
- Reliable: does not rely on specific behaviors, so it will not be blocked like meta-reflection or `dexfile`.
- Stable: `unsafe`, art structs and `setHiddenApiExemptions` are stable APIs.

[How it works (Chinese)](https://lovesykun.cn/archives/android-hidden-api-bypass.html)

## Integration

Gradle:


repositories {
    mavenCentral()
}
dependencies {
    implementation 'org.lsposed.hiddenapibypass:hiddenapibypass:5.0'
}

## Usage

1. Invoke a restricted method:
    
    HiddenApiBypass.invoke(ApplicationInfo.class, new ApplicationInfo(), "usesNonSdkApi"/*, args*/)
    
1. Invoke restricted constructor:
    
    Object instance = HiddenApiBypass.newInstance(Class.forName("android.app.IActivityManager$Default")/*, args*/);
    
1. Get all methods including restricted ones from a class:
    
    var allMethods = HiddenApiBypass.getDeclaredMethods(ApplicationInfo.class);
    ((Method).stream(allMethods).filter(e -> e.getName().equals("usesNonSdkApi")).findFirst().get()).invoke(new ApplicationInfo());
    
1. Get all non-static fields including restricted ones from a class:
    
    var allInstanceFields = HiddenApiBypass.getInstanceFields(ApplicationInfo.class);
    ((Method).stream(allInstanceFields).filter(e -> e.getName().equals("longVersionCode")).findFirst().get()).get(new ApplicationInfo());
    
1. Get all static fields including restricted ones from a class:
    
    var allStaticFields = HiddenApiBypass.getStaticFields(ApplicationInfo.class);
    ((Method).stream(allStaticFields).filter(e -> e.getName().equals("HIDDEN_API_ENFORCEMENT_DEFAULT")).findFirst().get()).get(null);
    
1. Get specific class method or class constructor
    
    var ctor = HiddenApiBypass.getDeclaredConstructor(ClipDrawable.class /*, args */);
    var method = HiddenApiBypass.getDeclaredMethod(ApplicationInfo.class, "getHiddenApiEnforcementPolicy" /*, args */);
    
1. Add a class to exemption list:
    
    HiddenApiBypass.addHiddenApiExemptions(
        "Landroid/content/pm/ApplicationInfo;", // one specific class
        "Ldalvik/system" // all classes in packages dalvik.system
        "Lx" // all classes whose full name is started with x
    );
    
    
    if you are going to add all classes to exemption list, just leave an empty prefix:
    
HiddenApiBypass.addHiddenApiExemptions("");
