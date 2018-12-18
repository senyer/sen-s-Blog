---
layout: post
title:  "【App扫码登录】的基本实现"
categories: java
tags: qrcode 
author: senyer
---

* content
{:toc}







## QRcode 实现APP二维码扫码登录

### 前言

>利用app的扫码功能，实现网页在app上扫码后，自动登录。





### 基本原理

1. 网页请求接口获取到uuid，作为当前登录用户的唯一身份。
2. 网页利用js生成qrcode，将信息存进二维码
3. 网页编写方法，定时请求登录，每次都要带上uuid。轮询（也可以通过长连接实现）
4. 手机app扫码，获取到里面的信息。同时，将app保存的用户信息，比如userId等，调用后台接口，进行绑定
5. 当网页轮询结果符合要求时，认证通过，进行登录
6. uuid信息可以保存在redis里面
7. 轮询可以用长连接代替，减少资源消耗
8. 可以对uuid进行加密处理，或者保存更多信息
9. 也可以直接通过后台生成二维码图片，传给前端，但是会增加服务器压力。


### 代码实现

``` java
package com.zy.ssm.common.controller;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;
import java.util.UUID;
import org.apache.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import com.alibaba.fastjson.JSONObject;
import com.zy.ssm.common.domain.User;
import com.zy.ssm.common.dto.Result;
import com.zy.ssm.common.dto.UserVO;
import com.zy.ssm.common.util.aes.AES;
import com.zy.ssm.core.constants.MsgEnum;
import com.zy.ssm.core.constants.SocketEnum;
import com.zy.ssm.personnel.service.UserService;
/*
 * QrCode二维码登录
 */
@RestController
@RequestMapping("/qrcode")
public class QrCodeLoginController {
	private static final Logger logger = Logger.getLogger(CommonController.class);
	
	@Autowired
	private UserService userService;
	
	
	//存储二维码维一标识
	//一般token和users可以存在redis里面
	public static Set<String> tokes = new HashSet<>();
	//存储toke绑定的用户
	public static Map<String,String> users = new HashMap<>();


	/**
	 * 生成二维码维一标识Toke
	 * @return
	 */
	@GetMapping("/uuid")
	public String generationToken(){
		String uid = UUID.randomUUID().toString();
		tokes.add(uid);
		return uid;
	}
	
	
	/**
	 * app扫码后将token与用户绑定
	 * @param passInfo
	 * @param token
	 * @return
	 */
	@RequestMapping("/binding")
	public Result<String>  determine(@RequestParam("passInfo")String passInfo,@RequestParam("token") String token){
		for (String t:tokes) {
			if(t.equals(token)){
				users.put(token,passInfo);
				//解密passInfo
				String passInfo_Decrypt =AES.AES_Decrypt(SocketEnum.SOCKET_SECRET_KEY.toString().getBytes(), passInfo.getBytes(), passInfo.getBytes().length);
				JSONObject json =(JSONObject) JSONObject.parse(passInfo_Decrypt);
				String loginName =json.getString("loginName");

			    User user = userService.selectByUsername(loginName);
				
				if(null==user){return new Result<String>(MsgEnum.USER_NOT_EXIST);}
				logger.info("用户与token绑定成功！+++++++++++++++++++++");
				return new Result<String>(MsgEnum.SUCCESS);
			}
		}
		return new Result<String>(MsgEnum.SUCCESS);
	}
 
	/**
	 * Axaj定时请求是否有用户扫描了二维码
	 * @param token
	 */
	@RequestMapping("/login")
	public Result<UserVO>  scanLogin(@RequestParam("token") String token){
		
		if(token==null||"".equals(token)){
			return new Result<UserVO>(MsgEnum.FALIURE);
		}
		
		String passInfo = users.get(token);
		
		if(passInfo==null||"".equals(passInfo)){
			return new Result<UserVO>(MsgEnum.FALIURE);
		}
		
		String passInfo_Decrypt =AES.AES_Decrypt(SocketEnum.SOCKET_SECRET_KEY.toString().getBytes(), passInfo.getBytes(), passInfo.getBytes().length);
		JSONObject json =(JSONObject) JSONObject.parse(passInfo_Decrypt);
		String loginName =json.getString("loginName");
		String password = json.getString("password");//密码

		UserVO userVO = null;

		try { 
			userVO = userService.login(loginName, password);
		}  catch (Exception exception) {
			logger.info("扫码登录异常+++++++++" + exception);
			return new Result<UserVO>(MsgEnum.FALIURE,null);
		}
		users.remove(token);
		tokes.remove(token);
		logger.info("扫码登录成功！+++++++++++++++++++++");
		return new Result<UserVO>(MsgEnum.SUCCESS,userVO);
	}
}

```






