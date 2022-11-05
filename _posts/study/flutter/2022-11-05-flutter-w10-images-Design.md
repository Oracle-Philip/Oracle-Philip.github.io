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

# Flutter w10 Images, Design

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

- Cookbook - Design
    - Add a Drawer to a screen

        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=d50dc9c0a6f9c11b013086a52e89c275"></iframe>

    - Display a snackbar

        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=2bdba336fc1da2777e8e5c305892abe5"></iframe>
        
        - Scaffold Messenger
    
        > 기존 SnackBar는 새 페이지로 이동하지 않는다. 이때 Scaffold Messenger를 사용한다. Scaffold Messenger는 모든 스캐폴드의 상위에서 작동하게 된다. 새로운 스캐폴드가 실행될 때마다 상위 Scaffold Messenger를 탐색하게 되고, looks up the tree for an ancestor scaffold and subscribes to snack bar events 한다.
        > ![](https://velog.velcdn.com/images/philipy/post/264153b4-0961-46ef-9ac0-2e703344d32b/image.png)
    
        ```dart
        ScaffoldMessenger.of(Context)

        final _scaffoldKey = GlobalKey<ScaffoldMessengerState>();

        MatreialApp(
            scaffoldMessengerKey: _scaffoldKey,
        )

        final ScaffoldMessengerState _scaffold = _scaffoldkey.currentState;

        scaffoldMessengerState.showSnackBar(
            SnackBar(...),
        )
        ```

    - Update the UI based on orientation

        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=3d006a7ba9a86ba0b0459bf77c7bf48e"></iframe>

    - Use themes to share colors and font styles

        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=39ebae7bb0dff37fefd73a62d5aaa57f"></iframe>
        
        
        ```dart
        MaterialApp(
        	theme: ThemeData(),
        	darkTheme: ThemeData.dark(),
        )
        ```
        
        ```dart
        MaterialApp(
        	colorScheme: ColorScheme.fromSwatch(
        		primarySwatch: Color.orange,
        	),
        	textTheme: GoogleFonts.emilysCandyTextTheme(),
        	scaffoldBackgroundColor: Colors.orange[100],
        )
        ```
        
        Theme 위젯으로 완전히 새로운 데이터를 제공하거나
        
        ```dart
        Theme(
        	data: ThemeData(...),
        	child: MyWidget(),
        )
        ```
        
        기존 테마를 복사하여 원하는 대로 활용할 수 있다.
        
        ```dart
        Theme(
        	data: Theme.of(context).copyWith(...),
        	child: MyWidget(),
        )
        ```

    - Work with tabs

        <iframe width="100%" height="800px" loading="lazy" src="https://dartpad.dev/?id=501569c1756d7f27608e57b8c3db4c55"></iframe>
