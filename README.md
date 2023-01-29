__Maps SDK for Android - By Google Document__

- thiết kế ứng dụng hiển thị Google Map theo cách Quick Start của Maps SDK for Android Quickstart
- truy cập URL [https://developers.google.com/maps/documentation/android-sdk/start#create-project](https://developers.google.com/maps/documentation/android-sdk/start#create-project) đọc hướng dẫn chi tiết

___

__CÁC BƯỚC ĐỂ TẠO NHANH 1 APP HIỂN THỊ GOOGLE MAP THEO HƯỚNG DẪN CỦA GOOGL CLOUD PLATFORM__

- __SET UP THE DEVELOPMENT ENVIRONMENT__
	- cài đặt Android Studio từ version Arctic Fox hoặc cao hơn
	- cài đặt Android Gradle Plugin từ version 7.0 hoặc cao hơn

- __SET UP AN ANDROID DEVICE__
	- để run app và sử dụng được Maps SDK for Android, thì thiết bị thật hay ảo phải cài đặt Android từ 4.0 hoặc cao hơn bao gồm đã được cài đặt Google Play Service (đã có Play Store)

- __CREATE GOOGLE MAP PROJECT IN ANDROID STUDIO__
	- mở Android Studio, click __Create New Project__ trong cửa sổ __Welcome to Android Studio__
	- trong cửa sổ __New Project__, trong mục __Phone and Tablet__ chọn __Google Maps Activity__ sau đó click __Next__
	- nhập các tùy chọn cho form __Google Maps Activity__
		- chọn __Language__ là __Java__ hay __Kotlin__ tùy vào nhu cầu
		- chọn version __Minimum SDK__ tương thích với thiết bị dùng để test, phải chọn version cao hơn version tối thiểu được yêu cầu cho version của __Maps SDK for Android__ hiện tại là __18.0.x__, nên chọn Minimum SDK Android API version 19 (Android 4.4, Kitkat) hoặc cao hơn
		- click __Finish__, Android Studio khởi động Gradle và build project
	- khi Gradle build hoàn tất, Android Studio sẽ mở 2 file __AndroidManifest.xml__ và __MapsActivity__, tên của activity có thể sẽ khác, ta có thể thay đổi trong quá trình setup project
	- __AndroidManifest.xml__ chứa hướng dẫn để get __Google Maps API Key__ và thêm nó vào file __local.properties__, __không thêm API Key vào file AndroidManifest.xml__ nó kém an toàn, thay vào đó làm theo hướng dẫn tạo __Google Cloud Project__ và tùy chỉnh thông số cho __API Key__

- __SET UP YOUR GOOGLE CLOUD PROJECT__
	- __Create Project__, truy cập URL [https://console.cloud.google.com/](https://console.cloud.google.com/) tạo mới 1 project
	- trong trang __New Project__ điền những thông tin cần thiết
		- __Project name__ (có thể để mặc định hoặc tùy chỉnh theo nhu cầu, có thể thay đổi project name bất cứ lúc nào)
		- __Project ID__ (có thể để mặc định hoặc tùy chỉnh theo nhu cầu khi click EDIT, mỗi ID chỉ dùng cho 1 project, sau khi click CREATE thì không thể thay đổi)
		- __Billing account__ (chọn 1 tài khoản thanh toán, nếu không có thiết lập tài khoản thanh toán hoặc chỉ có 1 tài khoản thanh toán tùy chọn Billing account sẽ không hiển thị)
		- __Location__ (nếu có tổ chức mà bạn muốn project liên kết đến nó, click Browse và chọn tổ chức đó, nếu không chọn No organization)
		- click CREATE

	- __Enable billing__ (khi ta cần deploy app, bắt buộc phải Enable billing, vì Google tính phí sử dụng API)
	- __Enable APIs__ (để sử dụng Google Maps Platform, bắt buộc phải kích hoạt APIs hoặc SDKs mà ta định sử dụng cho project), ở Google Cloud Project này ta cần sử dụng SDK Google Maps SDK for Android, tìm đến SDK này và click ENABLE để kích hoạt nó
	- __Get an API Key__, ở trang project vừa tạo, sau khi đã kích hoạt Google Maps SDK for Android, ta thực hiện việc lấy API Key của library Google Maps SDK for Android bằng các bước sau
		- truy cập đến trang __Credentials__ click __CREATE CREDENTIALS__ và chọn __API key__, thông báo popup hiển thị 1 API key được tạo cho project, click close đóng popup
		- thực hiện __Restrict the API__ (hạn chế API) 
			- ở trang Credentials, mục API key vừa tạo, chọn option Edit Api key
			- Key restrictions chọn Android apps
			- Restrict usage to your Android Apps click ADD, và thêm vào 2 thông tin
				- package name (com.*****.*****) chính là namespace trong gradle.build Module
				- SHA-1 signing-certificate fingerprint (chạy file signingReport ở thẻ Gradle Task/android/signingReport sau đó copy SHA-1)
				- click DONE
			- SAVE
	- __Add the API key to your app__ (ở phần này hướng dẫn lưu trữ API key 1 cách an toàn trong App)
		- trước tiên ta cần install plugin Secrets Gradle Plugin for Android, mở file build.gradle Project trong thẻ ``plugin`` thêm vào dòng code sau ``id 'com.google.android.libraries.mapsplatform.secrets-gradle-plugin' version '2.0.1' apply false``
		- mở file build.gradle Module trong thẻ ``plugin`` thêm vào dòng code sau ``id 'com.google.android.libraries.mapsplatform.secrets-gradle-plugin'`` sau đó click __Sync Now__
		- chọn view hiển thị project ở cấp Project mở file ``local.properties`` thêm vào dòng code sau, thay thế ``YOUR_API_KEY`` bằng API Key có được từ Google Cloud Project: ``MAPS_API_KEY=YOUR_API_KEY``
		- trong __AndroidManifest.xml__ ở thẻ ``application`` thêm vào dòng code meta-data sau 
		```xml
		<meta-data
    		android:name="com.google.android.geo.API_KEY"
    		android:value="${MAPS_API_KEY}" />
    	```
    	- đảm bảo file .gitignore đã chứa ``/local.properties`` và ``local.properties``
    - trong file build.gradle Module cài đặt thư viện ``implementation 'com.google.android.gms:play-services-maps:18.1.0'`` trong thẻ ``dependencies``
___

__ACTIVITIY & LAYOUT__

- __MapsActivity__
```java
public class MapsActivity extends FragmentActivity implements OnMapReadyCallback {

    private GoogleMap mMap;
    private ActivityMapsBinding binding;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        binding = ActivityMapsBinding.inflate(getLayoutInflater());
        setContentView(binding.getRoot());

        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);
    }

    /**
     * Manipulates the map once available.
     * This callback is triggered when the map is ready to be used.
     * This is where we can add markers or lines, add listeners or move the camera. In this case,
     * we just add a marker near Sydney, Australia.
     * If Google Play services is not installed on the device, the user will be prompted to install
     * it inside the SupportMapFragment. This method will only be triggered once the user has
     * installed Google Play services and returned to the app.
     */
    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;

        // Add a marker in Sydney and move the camera
        LatLng sydney = new LatLng(-34, 151);
        mMap.addMarker(new MarkerOptions().position(sydney).title("Marker in Sydney"));
        mMap.moveCamera(CameraUpdateFactory.newLatLng(sydney));
    }
}
```

- __activity_maps__
```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/map"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MapsActivity" />
```

- sau khi đã thực hiện đầy đủ các bước trên ta có thể run app để test hiển thị Google Map
1_show_map_successfully