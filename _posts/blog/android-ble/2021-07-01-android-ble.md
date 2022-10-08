# Android BLE function implement

### AndroidManifest.xml

```xml
<!--Android12 기준, 필요권한--> 
<uses-feature
    android:name="android.hardware.bluetooth_le"
    android:required="true" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.BLUETOOTH"
	  android:maxSdkVersion="30"/>
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"
    android:maxSdkVersion="30"/>
<uses-permission android:name="android.permission.BLUETOOTH_SCAN"
    android:usesPermissionFlags="neverForLocation"/>
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
```

### Google Android Bluetooth 개발자 문서 내용참조

📍 알아볼 내용 →  BLE 관련 class

1. BluetoothAdapter
2. BluetoothLeScanner
3. ScanSettings
4. ScanFilter
5. BluetoothManager
6. BluetoothGatt

- BluetoothAdapter class에 대해서 다음과 같이 설명하고 있다.
    - 로컬 장치 Bluetooth 어댑터를 나타낸다.
    - BluetoothAdapter를 사용하면
        - 장치 검색 시작
        - 연결된(페어링된) 장치 목록 쿼리
        - 알려진 MAC 주소를 사용하여 BluetoothDevice 인스턴스화
        - 다른 장치의 연결 요청을 수신 대기하는 BluetoothServerSocket 생성
        - Bluetooth LE 장치 검색 등을 할 수 있다.
    - 로컬 BluetoothAdapter를 얻으려면 BluetoothManager.getAdapter 메소드를 호출해야 한다.(JELLY_BEAN_MR1 이하에서는 getDefaultAdapter 메소드를 대신 사용한다)
    - startLeScan(BluetoothAdapter.LeScanCallback 콜백)을 사용하여 Bluetooth LE 장치 검색을 시작한다.
    - 이 클래스는 스레드로부터 안전하다.

- BluetoothLeScanner class에 대해서는 다음과 같이 설명하고 있다.
    - Bluetooth LE 장치에 대한 스캔 관련 작업을 수행하는 메서드를 제공한다.
    - 응용 프로그램은 ScanFilter를 사용하여 특정 유형의 Bluetooth LE 장치를 검색할 수 있다.
        - 또한 결과를 전달하기 위해 다양한 유형의 콜백을 요청할 수 있다.
    - BluetoothAdapter.getBluetoothLeScanner()를 사용하여 BluetoothLeScanner의 인스턴스를 가져온다.
    예) `mBLEScanner = mBluetoothAdapter.getBluetoothLeScanner();`

- ScanSettings class에 대해서는 다음과 같이 설명하고 있다.
    - Bluetooth LE 스캔 설정은 BluetoothLeScanner.startScan으로 전달되어 스캔 매개변수를 정의한다.
    예) `mBLEScanner.startScan(scanFilters, scanSettings, scanCallback);`
    - scan을 위한 5가지 모드가 있다. 일반적으로 SCAN_MODE_LOW_POWER를 사용한다.
        - SCAN_MODE_OPPORTUNISTIC
            - 특별한 블루투스 LE 스캔 모드이다.
            - 이 스캔 모드를 사용하는 애플리케이션은 BLE 스캔 자체를 시작하지 않고 수동적으로 다른 스캔 결과를 수신한다.
        - SCAN_MODE_LOW_POWER
            - 저전력 모드에서 Bluetooth LE 스캔을 수행한다.
            - 전력을 가장 적게 소모하는 기본 스캔 모드이다.
            - 이 모드는 스캔 응용 프로그램이 포그라운드에 있지 않은 경우 적용된다.
        - SCAN_MODE_BALANCED
            - 밸런스 전력 모드에서 Bluetooth LE 스캔을 수행한다.
            - 스캔 결과는 스캔 주파수와 전력 소비 간에 적절한 균형을 제공하는 속도로 반환된다.
        - SCAN_MODE_LOW_LATENCY
            - 가장 높은 듀티 사이클을 사용하여 스캔한다.
            - 애플리케이션이 포그라운드에서 실행 중일 때만 이 모드를 사용하는 것이 좋다.
        - SCAN_MODE_AMBIENT_DISCOVERY
            - 주변 검색 모드에서 Bluetooth LE 스캔을 수행한다.
            - 이 모드는 스캔 대기 시간과 전력 소비 사이에 좋은 균형을 제공하는 밸런스 모드보다 듀티 사이클이 낮고 스캔 간격이 빠르다.

- ScanFilter class에 대해서는 다음과 같이 설명하고 있다.
    - 블루투스 LE 스캔 결과 필터링 기준으로 ScanFilter를 사용하면 클라이언트가 관심 있는 결과로만 스캔 결과를 제한할 수 있다.
    - 아래 필드에 대한 현재 필터링이 지원된다.
        - 장치에서 실행 중인 블루투스 gatt 서비스를 식별하는 서비스 UUI
        - 원격 Bluetooth LE 장치의 이름
        - 원격 장치의 Mac 주소
        - 서비스 데이터(서비스와 관련된 데이터)
        - 제조업체별 데이터(특정 제조업체와 관련된 데이터)
    
- BluetoothManager
    - BluetoothAdapter의 인스턴스를 획득하고 전반적인 Bluetooth 관리를 수행하는 데 사용되는 고급 관리자이다.
    - Context.BLUETOOTH_SERVICE와 함께 Context.getSystemService(String)를 사용하여 BluetoothManager를 만든 다음 getAdapter를 호출하여 BluetoothAdapter를 가져온다.
- BluetoothGatt
    - Bluetooth GATT 프로필용 공개 API 이다.
    - 통신을 가능하게 하는 Bluetooth GATT 기능을 제공한다.
    - Bluetooth Smart 또는 Smart Ready 장치 사용한다.
    - 원격 주변 기기에 연결하려면 BluetoothGattCallback 생성해야한다.
    - BluetoothDevice connectGatt를 호출하여 이 클래스의 인스턴스를 가져올 수 있다.

### 장치 스캔 및 연결을 위한 샘플코드

```java
private ScanCallback mScanCallback = new ScanCallback() {
    @Override
    public void onScanResult(int callbackType, ScanResult result) {
        try {
						/*
						 *   result.getDevice() 호출을 통해 원하는 기능구현을 할 수 있다.  
		         *   result.getDevice().getName();
             *   result.getDevice().getAddress();
						 */
            }
        } catch (NullPointerException e){}
        finally {

        }
    }
};

private void bleScanSetting(){
    mHandler = new Handler();

    final BluetoothManager bluetoothManager =
            (BluetoothManager) getSystemService(Context.BLUETOOTH_SERVICE);

    mBluetoothAdapter = bluetoothManager.getAdapter();

    if (mBluetoothAdapter == null) {
        finish();
        return;
    }

    mBLEScanner = mBluetoothAdapter.getBluetoothLeScanner();
    if (mBLEScanner == null) {
        finish();
        return;
    }

    mScanSettings = new ScanSettings.Builder();
    mScanSettings.setScanMode(ScanSettings.SCAN_MODE_LOW_LATENCY);
    scanSettings = mScanSettings.build();

    scanFilters = new Vector<>();
    ScanFilter.Builder scanFilter = new ScanFilter.Builder();
    ScanFilter scan = scanFilter.build();
    scanFilters.add(scan);
}

private void scanLeDevice(final boolean enable) {
    if (enable) {
        mHandler.postDelayed(new Runnable() {
            @Override
            public void run() {

           }
        }, time);
        mBLEScanner.startScan(scanFilters, scanSettings, mScanCallback);
    } else {
        mBLEScanner.stopScan(mScanCallback);
    }
}
```

### BluetoothGattCallback Instance & override callback method

```java
private final BluetoothGattCallback mGattCallback = new BluetoothGattCallback() {
        @Override
        public void onConnectionStateChange(BluetoothGatt gatt, int status, int newState) {
            switch(newState)
            {
                case STATE_CONNECTED :
                    mBluetoothGatt.discoverServices();
                    break;
                case STATE_DISCONNECTED :
                    if(status != 133 && status != 62 || disconnectCount >= 2) {
                        connect_state.postValue(false);
                        disconnectCount = 0;
                        disconnect();
                        close();
                    }
                    else{ /*reconnection*/
                        disconnectCount++;
                        disconnect();
                        close();
                    }
                    break;
            }
        }
        @Override
        public void onServicesDiscovered(BluetoothGatt gatt, int status) {
            if (status == BluetoothGatt.GATT_SUCCESS) {
                String uuid = null;
                for(BluetoothGattService gattService : mBluetoothGatt.getServices()){
                    uuid = gattService.getUuid().toString();
                    if(uuid.equals(BLE_DEVICE.toString())){
                        List<BluetoothGattCharacteristic> gattCharacteristics =
                                gattService.getCharacteristics();
                        for (BluetoothGattCharacteristic gattCharacteristic : gattCharacteristics) {
                            uuid = gattCharacteristic.getUuid().toString();
                            if(uuid.equals( )){

                            }
                            if(uuid.equals( )){

                            }
                        }
                    }
                }
            } 
        }
        @Override
        public void onCharacteristicChanged(BluetoothGatt gatt,
	                      BluetoothGattCharacteristic characteristic) {

        }

        @Override
        public void onCharacteristicWrite(final  BluetoothGatt gatt,
                                          final  BluetoothGattCharacteristic characteristic, int status) {
            if (status == BluetoothGatt.GATT_SUCCESS) {
                String senddata = null;
                final byte[] data = characteristic.getValue();
                if (data != null && data.length > 0) {
                    final StringBuilder stringBuilder = new StringBuilder(data.length);
                    for (byte byteChar : data)
                        stringBuilder.append(String.format("0x%02X ", byteChar));
                    senddata = new String(data) + "\n" + stringBuilder.toString();
                }
            }
        }
    };
```

### 장치 연결해제를 위한 샘플코드

```java
public boolean connect(final String address, final String devicename) {
    if (mBluetoothAdapter == null || address == null) {
        return false;
    }

    if (mBluetoothDeviceAddress != null && address.equals(mBluetoothDeviceAddress)
            && mBluetoothGatt != null) {
        if (mBluetoothGatt.connect()) {
            return true;
        } else {
            return false;
        }
    }

    final BluetoothDevice device = mBluetoothAdapter.getRemoteDevice(address);
    if (device == null) {
        return false;
    }
    mBluetoothGatt = device.connectGatt(null, false, mGattCallback);
    return true;
}

public void disconnect() {
    if (mBluetoothAdapter == null || mBluetoothGatt == null) {
        return;
    }
    mBluetoothGatt.disconnect();
}

public void close() {
    if (mBluetoothGatt == null) {
        return;
    }
    mBluetoothGatt.close();
    mBluetoothGatt = null;
    mBluetoothDeviceAddress = null;
}
```
