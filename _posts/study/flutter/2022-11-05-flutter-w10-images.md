---
layout: post
title: 'Flutter_week10'
subtitle:   "."
date: '2022-11-05 00:00:51 +0900'
categories:
    - study
    - flutter
tags:
    - flutter
comments: true
published: true
hide_last_modified: true
sitemap: false
---

# Flutter w10 _Images

- Cookbook - Images
    - Display images from the internet
        
        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=1b1711db66551c2f0960fce2d358dd99"></iframe>
        
    
    - Work with cached images
        
        ```dart
        CatchedNetworkImage(
        	imageUrl: "https://my-image.jpeg"
        )
        ```
        
        - CatchedNetworkImage 위젯은 다양한 맞춤 설정이 가능하다.
            - placeholder 파라미터를 사용해서 이미지가 다운로드되는 동안 나타낼 대상을 관리할 수 있다.
                
                ```dart
                CachedNetworkImage (
                	imageUr1: "https://my-image. jpg", 
                	placeholder: (context, ur1) => Text('Loading Surl'),
                )
                ```
                
        
        - progressIndicator Burilder 파라미터로 업데이트를 할 수 있다.
            
            ```dart
            CachedNetworkImage (
            	imageUrl: "https://my-image. jpg",
            	progressIndicatorBuilder: (_, url, download) {
            	if (download.progress != null) {
            		final percent = download.progress! * 100;
            		return Text ('$percent% done loading');
            	}
            	return Text ('Loaded $url!');
            	},
            )
            ```
            
        
        - BoxDecorations 이미지 속성을 이용한 캐시 작업
            - NetworkImage 객체는 위젯이 아니라 이미지 제공자이기 때문에 CatchedNetworkImage를 제자리에 넣을 수 없다.
                - 이때 CachedNetworkImageProvider를 사용하면 된다.
                    
                    ```dart
                    Container(
                    	decoration: BoxDecoration(
                    		image: CachedNetworkImageProvider(
                    			'https://my-image.jpg',
                    			),
                    		),
                    	)
                    ```
