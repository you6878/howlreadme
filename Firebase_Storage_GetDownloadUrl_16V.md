# GetDownloadUrl 방식

최근 GetDownloadUrl 방식이 변경되었습니다. 그 이유는 사진을 업로드 할 때마다. GetDownloadUrl 주소를 받는 방식이 서버의 부담을 주기 때문에 필요할 때문 URL 주소를 요청하도록 변경하는 것으로 보입니다.

이전에는 **사진 업로드 후 바로 URL 주소**가 넘어 왔습니다. 하지만 이번부터는 **파일 경로**를 적어야지 URL 주소를 받을 수 있습니다. 요약해서 말하면 2단계로 진행을 해야 파일 주소를 받아 올 수가 있습니다.

첫번째 단계는 사진 업로드 입니다. 업로드를 할때 경로를 지정해서 업로드를 합니다.
1. 단계 : 경로지정
```swift
var storageRef =. storage?.reference?.child("images")?.child(imageFileName)
```

저장할 경로가 지정된 값이 **storageRef**변수에 저장이 됩니다.
 2. 단계 : 업로드
```swift
var photoUri = [사진 URL]
storageRef?.putFile(photoUri!!)?.addOnSuccessListener {
[사진 주소 받아오는 코드 넣기]
}
```
사진 업로드 하는 방법은   **storageRef**에 **putFile**을 이용하면 파일이 업로드 됩니다. 하지만 서버와의 동기화를 기다리고 사진을 받아와야 하기 때문에 **addOnSuccessListener**안에서 사진을 받아오는 코드를 넣어야합니다.
3. 단계 : 경로 가져오기

```swift
var photoUri = [사진 URL]
storageRef?.putFile(photoUri!!)?.addOnSuccessListener {
	storageRef.downloadUrl.addOnSuccessListener { uri ->
		var imageUrl = uri.toString()
	}
}
```
사진을 가져오는 방법은 1단계 경로지정의 **storageRef**에다가 **getDownlaodUrl**을 통해서 받아오면 됩니다. 서버와 동기화 이전에 받아오게되면 에러가 발생되기 때문에 반드시 **addOnSuccessListener**안에서 호출 하시길 바랍니다.

물론 이렇게 **Callback**방식으로 **addOnSuccessListener**안에서 **downloadUrl** 호출하는 방식이 있지만 코드의 가독성을 위해서 자바스크립트의 **Promise** 방식으로도 호출 할 수 있습니다. 물론 자바스크립트가 익숙하지 않으신분들은 **Promise** 방식을 굳이 따라하실 필요는 없습니다.

 - Promise방식
```swift
var photoUri = [사진 URL]
storageRef?.putFile(photoUri!!)?.continueWithTask 
	{ task: Task<UploadTask.TaskSnapshot> ->
            return@continueWithTask storageRef.downloadUrl
        }?.addOnSuccessListener { uri ->
			var imageUrl = uri.toString()
	}
```



