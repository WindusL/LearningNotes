---
title: Java Properties文件
date: 2014-09-28
categories: 软件开发
tags:
	- JAVA
	- Properties
---

Java开发者对Properties一定再熟悉不过了,它是JAVA项目里特有的一种配置文件，Properties 继承于 Hashtable，所以可对 Properties 对象应用 put 和 putAll 方法。但不建议使用这两个方法，因为它们允许调用者插入其键或值不是 String 的项，应该使用 setProperty 方法。如果在“不安全”的 Properties 对象（即包含非 String 的键或值）上调用 store 或 save 方法，则该调用将失败。  

最近做项目想在项目中实现动态配置文件的功能，本来以为水到渠成的事，结果遇到了一点小麻烦，现将问题记录下来。

<!--more-->

通常用法：  

```java
package com.test;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Properties;

/**
 * 
 * @author Windus
 * @version v1.0
 *
 */
public class PropertiesUtil {

	private Properties properties;
	// properties文件路径
	private String proPath;

	public PropertiesUtil(String proPath) {
		this.proPath = proPath;
		loadProperties();
	}

	/**
	 * 加载Properties文件
	 * 
	 * @param proPath
	 */
	public void loadProperties() {
		try {
			properties = new Properties();
			InputStream is = this.getClass().getResourceAsStream(proPath);
			properties.load(is);
			is.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 读取配置文件键值
	 * 
	 * @param key
	 * @return
	 */
	public String getProperties(String key) {
		if (properties != null) {
			return properties.getProperty(key);
		} else {
			return null;
		}
	}

	/**
	 * 设置键值
	 * 
	 * @param key
	 * @param value
	 */
	public void setProperties(String key, String value) {
		try {
			if (properties != null) {
				// 设置键值
				properties.setProperty(key, value);
				OutputStream fos = new FileOutputStream(proPath);
				properties.store(fos,
						new SimpleDateFormat("yyyy-MM-dd").format(new Date()));
				fos.flush();
				fos.close();
			}
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}

	public static void main(String[] args) {
		PropertiesUtil propertiesUtil = new PropertiesUtil("/test.properties");
		//打印当前键值
		System.out.println(propertiesUtil.getProperties("name"));
		//设置键值
		propertiesUtil.setProperties("name", "windus");
		//打印修改后的值
		System.out.println(propertiesUtil.getProperties("name"));

		//重新加载properties文件读取
		PropertiesUtil propertiesUtil = new PropertiesUtil("/test.properties");
		System.out.println(propertiesUtil.getProperties("name"));
	} 
}
```

当我在启动Tomcat后，重新在前台传入key值后调用设置方法后重新读取，如main方法中一样调用，发现每次修改后的properties文件本身确实改变了，但读取出来的值确不是实时更新的，原因在于getProperties每次读取都是从内存中读取，而对于文件的更新后没有实时加载到内存中。

经过轮番的修改……测试……不行；修改……测试……不行；…… ……后：得出解决方法，问题出现在加载properties文件的方法上：如上37行

```java
InputStream is = this.getClass().getResourceAsStream(proPath);
//修改为
InputStream is = new BufferedInputStream(new FileInputStream(getClass().getResource(proPath).getPath()));
```

getResourceAsStream每次读取来自内存中，所以可以通过getResource(proPath).getPath()来获取properties文件路径，然后读取到输入流中实时加载properties文件，此时即可得到每次改变后的值。



