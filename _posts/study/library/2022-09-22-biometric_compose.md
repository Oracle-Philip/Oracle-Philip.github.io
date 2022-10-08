---
layout: post
title: 'Android Biometric'
subtitle:   "."
date: '2022-09-22 12:45:51 +0900'
categories:
    - study
    - library
tags:
    - library
comments: true
published: true
---

# Biometric_Android Study

![](https://velog.velcdn.com/images/philipy/post/9b5809ff-d689-4248-b85d-7b184c070053/image.jpeg)


- 필요권한 AndroidManifest에 명시한다.
    
    ```kotlin
    <uses-permission
        android:name="android.permission.USE_BIOMETRIC" />
    ```
    

- AuthenticationCallback Object
    
    ```kotlin
    private val authenticationCallback: BiometricPrompt.AuthenticationCallback
    get() = object : BiometricPrompt.AuthenticationCallback(){
        override fun onAuthenticationError(errorCode: Int, errString: CharSequence?) {
            notifyUser("Authentication error: $errString")
            super.onAuthenticationError(errorCode, errString)
        }
    
        override fun onAuthenticationHelp(helpCode: Int, helpString: CharSequence?) {
            super.onAuthenticationHelp(helpCode, helpString)
        }
    
        override fun onAuthenticationSucceeded(result: BiometricPrompt.AuthenticationResult?) {
            notifyUser("Authentication Succeeded")
            super.onAuthenticationSucceeded(result)
        }
    
        override fun onAuthenticationFailed() {
            super.onAuthenticationFailed()
        }
    }
    ```
    
- 생체인증 팝업창
    
    ```kotlin
    fun authenticateUser(){
        val biometricPrompt = BiometricPrompt.Builder(this)
            .setTitle("Biometric Demo")
            .setSubtitle("Authentication is required to continue")
            .setDescription("This app uses biometric authentication to protect your data.")
            .setNegativeButton("Cancel", this.mainExecutor,
            DialogInterface.OnClickListener{dialogInterface, i->
    notifyUser("Authentication cancelled")
    }).build()
        biometricPrompt.authenticate(getCancellationSignal(),mainExecutor, authenticationCallback)
    }
    ```
    
- 권한확인 함수
    
    ```kotlin
    private fun checkBiometricSupport(): Boolean{
        val keyguardManager = getSystemService(Context.KEYGUARD_SERVICE)
            as KeyguardManager
    
        if(!keyguardManager.isKeyguardSecure){
            notifyUser("Lock screen security not enabled in Setting")
            return false
        }
    
        if(ActivityCompat.checkSelfPermission(
                this, Manifest.permission.USE_BIOMETRIC) !=
                    PackageManager.PERMISSION_GRANTED){
                        notifyUser("Fingerprint authentication permisson not enabled")
                        return false
        }
    
        return if(packageManager.hasSystemFeature(
                PackageManager.FEATURE_FINGERPRINT)){
            true
        } else true
    }
    ```
    
- Compose 이용한 버튼중앙 배치 코드
    
    ```kotlin
    @Composable
        fun widget_position(){
            Column(
                modifier = Modifier
                    .fillMaxWidth()
                    .fillMaxHeight(),
                verticalArrangement = Arrangement.Center,
                horizontalAlignment = Alignment.CenterHorizontally
            ){
                SimpleButton()
            }
        }
    
        @Composable
        fun SimpleButton(){
            //val context = LocalContext.current
            Button(onClick = {
                authenticateUser()
            }){
                Text(text = stringResource(id = R.string.authenticate))
            }
        }
    ```
    
- CancellationSignal object
    
    ```kotlin
    private var cancellationSignal: CancellationSignal? = null
    
        private fun getCancellationSignal() : CancellationSignal {
            cancellationSignal = CancellationSignal()
            cancellationSignal?.setOnCancelListener {
                notifyUser("Cancelled via signal")
            }
            return cancellationSignal as CancellationSignal
        }
    ```
    
    
[안드로이드 생체인증 개발문서](https://developer.android.com/training/sign-in/biometric-auth)

[샘플코드](https://github.com/Oracle-Philip/Android_Study/tree/main/Biometric_compose)