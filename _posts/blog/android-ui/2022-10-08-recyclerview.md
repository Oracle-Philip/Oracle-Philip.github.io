---
layout: post
title: 'RecyclerView - Compose활용'
subtitle:   "."
date: '2022-10-08 12:45:51 +0900'
categories:
    - blog
    - android-ui
tags:
    - compose
comments: true
published: true
---

# RecyclerView

- 리사이클러 뷰는 중첩된 스크롤을 구현하는 리스트 부의 세련되고 유연한 버전이다.
- 리사이클러 뷰는 목록을 만드는 RecyclerView 클래스만으로는 화면에 아무것도 출력되지 않는다. 그러므로 다음 구성 요소를 이용해야 한다.
    - ViewHolder(필수) : 항목에 필요한 뷰 객체를 가짐
    - Adapter(필수) : 항목을 구성
    - LayoutManager(필수) : 항목을 배치
    - ItemDecoration(옵션)
- 리사이클러 뷰에서는 어댑터로 데이터에 접근할 수 있다.
    - ListView와 달리 배열 어댑터 같은 내장 안드로이드 어댑터를 사용하지 않는다.
    - 리사이클러 뷰에서는 사용하려는 데이터에 맞는 어댑터를 직접 구현해야 한다.
        - 데이터의 유형은 무엇인지
        - 뷰를 어떻게 생성하는지
        - 데이터를 어떻게 뷰에 연결하는지 등을 지정해야한다.
    - 리사이클러 뷰의 항목 위치는 레이아웃 관리자로 지정한다.
    - 데이터 소스 → 뷰홀더 → 어댑터 → 레이아웃 메니저 → 리사이클러 뷰를 통해 나타낸다.

- Compose를 이용한 RecyclerView 예제
    - Compose는 기존 명령형 방식에 xml 대신 선언형 방식으로 Ui를 구현하는 방식이다.
    - RecyclerView 구현에 있어 다음과 같이 차이가 있다. → LazyColumn 혹은 LazyRow를 쓴다.
        - MainActivity
        
        ```kotlin
        class MainActivity : ComponentActivity() {
            override fun onCreate(savedInstanceState: Bundle?) {
                super.onCreate(savedInstanceState)
                setContent {
                    RecyclerViewTheme {
                        MyApp()
                    }
                }
            }
        }
        
        @Composable
        fun MyApp(){
            Scaffold(
                content = {
                    HomeContent()
                }
            )
        }
        
        @Composable
        fun HomeContent(){
            val books = remember { DataProvider.bookList }
            LazyColumn(
                contentPadding = PaddingValues(horizontal = 16.dp, vertical = 8.dp)
            ) {
                items(
                    items = books,
                    itemContent = {
                        BookListItem(book = it)
                    })
            }
        }
        
        @Composable
        fun BookListItem(book: Book) {
            Card(
                modifier = Modifier
                    .padding(horizontal = 8.dp, vertical = 8.dp)
                    .fillMaxWidth(),
                elevation = 2.dp,
                backgroundColor = Color.White,
                shape = RoundedCornerShape(corner = CornerSize(16.dp))
            ) {
                Row {
                    BookImage(book)
                    Column(
                        modifier = Modifier
                            .padding(16.dp)
                            .fillMaxWidth()
                            .align(Alignment.CenterVertically)
                    ) {
                        Text(text = book.title, style = typography.h6)
                        Text(text = book.author, style = typography.caption)
                    }
                }
            }
        }
        
        @Composable
        fun BookImage(book: Book){
            Image(
                painter = painterResource(id = book.ImageId),
                contentDescription = null,
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .padding(8.dp)
                    .size(84.dp)
                    .clip(RoundedCornerShape(corner = CornerSize(16.dp)))
            )
        }
        ```
        
        - data class
        
        ```kotlin
        data class Book(
            val id: Int,
            val title: String,
            val author: String,
            val ImageId: Int = 0
        )
        ```
        
        - data provider(데이터 소스)
        
        ```kotlin
        object DataProvider {
        
            val bookList = listOf(
                Book(
                    id = 1,
                    title = "Kotlin in action",
                    author = "A",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 2,
                    title = "Kotlin in action2",
                    author = "B",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 3,
                    title = "Kotlin in action3",
                    author = "C",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 4,
                    title = "Kotlin in action4",
                    author = "D",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 3,
                    title = "Kotlin in action3",
                    author = "C",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 3,
                    title = "Kotlin in action3",
                    author = "C",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 3,
                    title = "Kotlin in action3",
                    author = "C",
                    ImageId = R.drawable.kotlin_in_action
                ),
                Book(
                    id = 3,
                    title = "Kotlin in action3",
                    author = "C",
                    ImageId = R.drawable.kotlin_in_action
                ),
            )
        }
        ```
        
        - 화면
        
        ![](https://velog.velcdn.com/images/philipy/post/15cf1dcd-8d7d-45a2-b8fa-c22ab8072817/image.png)

