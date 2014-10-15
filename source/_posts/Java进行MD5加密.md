title: Java进行MD5加密
tags:
  - code
date: 2011-12-09 19:56:10
---

直接上代码，JDK6已经有一个方便点的类帮助简化步骤：

<pre class="brush:java">package com.cssor.test;

import java.io.UnsupportedEncodingException;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class MD5 {
	/** 
     * MD5 加密 
     */  
    private static String getMD5Str(String str) {  
        MessageDigest messageDigest = null;  

        try {  
            messageDigest = MessageDigest.getInstance("MD5");  

            messageDigest.reset();  

            messageDigest.update(str.getBytes("UTF-8"));  
        } catch (NoSuchAlgorithmException e) {  
            System.out.println("NoSuchAlgorithmException caught!");  
            System.exit(-1);  
        } catch (UnsupportedEncodingException e) {  
            e.printStackTrace();  
        }  

        byte[] byteArray = messageDigest.digest();  

        StringBuffer md5StrBuff = new StringBuffer();  

        for (int i = 0; i &lt; byteArray.length; i++) {              
            if (Integer.toHexString(0xFF &amp; byteArray[i]).length() == 1)  
                md5StrBuff.append("0").append(Integer.toHexString(0xFF &amp; byteArray[i]));  
            else  
                md5StrBuff.append(Integer.toHexString(0xFF &amp; byteArray[i]));  
        }  

        return md5StrBuff.toString();  
    }  

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		System.err.println(MD5.getMD5Str("a"));
	}

}</pre>

来源： http://warren.iteye.com/blog/107386