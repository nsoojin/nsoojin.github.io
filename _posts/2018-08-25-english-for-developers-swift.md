---
layout: post
title: "개발자를 위한 영어 for Swift"
categories: []
tags:
- iOS
- english grammar
- swift conventions
status: publish
type: post
published: true
thanks: 
- 이승원
- 김결
- 강한용
- 김영
meta: {}
---

1979년에 발간됐지만 여전히 프로그래밍 입문서로 유명한 *Structures and Interpretation of Computer Programs*의 도입부에 이런 말이 있다.
>Programs should be written for people to read, and only incidentally for machines to execute.

>(내맘대로 의역) 프로그램은 사람들에게 읽히기 위한 목적으로 만들어져야 하고, 우연히 컴퓨터가 실행할 수 있다면 더욱 좋다.

신입으로 입사했던 첫 해, 협업을 해보니 코드를 쓰는 시간만큼이나 읽는 시간도 엄청 많다는 걸 알게 되었다. 동료(또는 나)의 코드가 잘 읽히면 내 작업 속도가 빨라졌고 흐름을 이해하기 어려운 코드에 부딪히면 느려졌다. 그래서 그때부터 독자의 입장도 고려해야겠다는 생각을 하게 됐다. 소프트웨어의 **사용자**와 **독자** 양 쪽 모두에 감정 이입하는게 프로그래머의 일인 것 같다.

## 영어와 Naming Conventions

영어가 외국어인 우리는 컨벤션도 지켜야하고 영어도 고민해야하는 이중고에 처해 있다고 생각할수도 있지만 딱히 억울해 할 것도 없다. 영어가 모국어인 개발자가 전세계에 얼마나 될까. 미국인은 전세계 인구의 5%도 안된다. 그렇기에 완벽한 영어를 할 필요도 없고 회화 실력과는 더더욱 무관하다(해외 취업할 때는 큰 도움이 되겠지만!). **그럼에도 프로그래머 업무의 절반 이상은 이름 짓기라고 하니 알아두면 유용한 영어 문법과 스위프트 컨벤션을 일곱 가지로 분류했다.**

1. 동사의 변형
2. 단수와 복수
3. 타입별 Naming Conventions
4. Bool 변수
5. 중복 제거
6. 스위프트의 getter
7. `fetch`, `request`, `perform` 비교


## #1 동사의 변형

영어에서 동사는 세 가지 형태로 사용된다. ***동사원형 - 과거형 - 과거분사***.  
동사원형은 '~한다'라는 행위의 의미, 과거형은 '~했다'라는 과거의 의미, 과거 분사는 수동형의 의미이다. 그런데 프로그래밍을 할 때는 과거의 의미를 쓸 일이 거의 없으므로 일단은 무시해도 좋다.

|동사 원형|~~과거형~~|과거 분사|
|-------|-------|-------|
|request(요청하다)|~~requested(요청했다)~~|requested(요청된)|
|make(만들다)|~~made(만들었다)~~|made(만들어진)|
|hide(숨다)|~~hid(숨었다)~~|hidden(숨겨진)|

### ✏️ 동사 원형
동사 원형은 크게 세 군데에 사용된다. 
- 함수 및 메서드
- Bool 변수 (조동사 + 동사원형)
	- `canBecomeFirstResponder`, `shouldRefresh` 등
- Life Cycle 관련 delegate 메서드 (조동사 + 동사원형) 
	- `didFinish`, `willAppear`, `didComplete` 등

### ✏️ 과거 분사
동사의 의미를 형용사로 쓰고 싶을 때는 과거 분사로 변형해서 사용해야 한다.
- 명사 수식
	- `requestedData`, `hiddenView`
- Bool 변수
	- `isHidden`, `isSelected`

⚠️ 동사와 명사가 똑같이 생긴 단어도 있다   
`request`(요청하다 혹은 요청), `start`(시작하다 혹은 시작점), `play`(재생하다 혹은 재생) 등

## #2 단수와 복수

보통 인스턴스 하나는 단수형으로 이름 짓고 어레이 타입은 복수형으로 이름 지어준다. 복수형으로 이름 지어주는 것만으로 배열이라는 걸 알 수 있으니 특수한 경우가 아니라면 `List`나 `Array`를 뒤에 붙여줄 필요는 없지 않을까 싶다.

```swift
let album: Album
let albums: [Album]

let albumList: [Album]
let albumArray: [Album]
```

#### ✏️ 불규칙 복수형
복수형을 쓸 때 고려해야할 건 단어마다 복수형이 조금씩 다르다는 것이다. 대다수의 경우 -s를 붙이면 되지만 -es일때도 있고 -ies일수도 있고 새로운 단어로 바뀔 수도 있다. 안 바뀔수도..

|단수|복수|
|-------|-------|
|view|views|
|box|boxes|
|half|halves|
|category|categories|
|child|children|
|person|people|
|index|indices 혹은 indexes|
|datum|data|
|information|information|

## #3 타입별 Naming Conventions

좀 전에 배열 타입의 변수에 굳이 `List`나 `Array`라는 타입을 명시하지 않는다고 했는데 Cocoa Touch 프레임워크 전반에 걸쳐 타입을 변수명에 명시해 주는 경우가 있다. `URL`, `UIImage`, `Date`, `Size`, `Data`처럼 상대적으로 raw한 데이터 타입이라면 명시해 주는 것 같고, 새로 정의한 타입이라면 써줄 필요가 없을 것 같다.

```swift
var fullSizeImageURL: URL?     //PHContentEditingInput
var thumbnailURL: URL?         //MLMediaObject
var referenceURL: URL          //SCNReferenceNode

var referenceImage: ARReferenceImage?   //ARImageAnchor
var iconImage: NSImage?                 //MLMediaGroup

var physicalSize: CGSize               //ARReferenceImage
var startDate: Date 
var adjustmentData: PHAdjustmentData?  //PHContentEditingInput
```

### ✏️ `id` vs `Id` vs `ID` vs `identifier`

프레임워크 문서를 보다가 또 한가지 발견한 것. Cocoa Touch에서는 대부분 `identifier`를 쓰고 별도의 타입을 쓸 때는 `ID`를 쓴다. 근데 이것도 절대 규칙은 아닌것 같고.. *"대체로"* 그렇다.

```swift
var formatIdentifier: String { get }          //PHAdjustmentData  
var identifier: String { get }                //CLRegion          
var identifier: String { get }                //MLMediaGroup 
var objectID: NSManagedObjectID { get }       //NSManagedObject
var recordID: CKRecordID { get }              //CKRecord          
var uniqueID: String? { get }                 //AVMetadataGroup 
```

## #4 Bool 변수

Bool 변수명을 지을 때 알아두면 좋은 문법 몇 가지는 [Bool 변수 이름 제대로 짓기 위한 최소한의 영어 문법](https://soojin.ro/blog/naming-boolean-variables)에서 자세히 다뤘었고, 컨벤션적인 측면에서도 고려할 것이 몇 가지 있다.

### ✏️ `isSelected` vs `selected`

문법적으로만 보면 `isSelected`과 `selected` 둘 다 맞다고 할 수 있으니 각 언어나 개발 플랫폼마다의 컨벤션을 따르면 되겠다. 스위프트에서는 `is`를 써주는 것이 컨벤션이다보니 맞춰주면 좋을 것 같다. 

### ✏️ `isEnabled` vs `isDisabled`

Bool 타입의 경우 반대어를 써서 동일한 기능을 하는 이름을 만들 수 있다. 둘 중 뭐가 나은지 정답은 없지만 고민해볼 만한 기준은 있다. 조건문에 not 연산자를 붙이는 것 보다는 없는 것이 가독성에 조금 더 좋다. 따라서 로직이 추가되는 경우가 true가 되도록 하는 단어를 선택하면 어떨까. 또 하나는 변수의 디폴트 값이 true가 되도록 하는 것이다. `UIView`의 경우 **'보여지는'** 상태가 디폴트임에도 `isHidden`을 쓰고 있어서 종종 뇌에 혼란이 올 때가 있다.

```swift
if isEnabled { ... }
if !isEnabled { ... }

tableView.isVisible = !items.isEmpty  //"tableView가 보일 때는 items가 비어있지 않을 때"
tableView.isHidden = items.isEmpty    //"tableView가 숨어있을 때는 items가 비어있을 때"
```

## #5 중복 제거

의미가 중복되는 단어가 많으면 읽기 좋은 글이 아니다. 코드도 마찬가지로 불필요하게 의미가 중복되는 것을 빼버리면 간결하고 읽기 좋은 코드가 될 것이다. 

```swift
struct User {
  let userID: String
}
```

`userID`는 떼어놓고 보면 의미가 명확하고 잘 지은 변수명이지만 실제 쓰일 때는 User 인스턴스와 함께 쓰일 것이기 때문에 굳이 'user'라는 의미를 포함시키지 않아도 된다. `identifier`라고만 해줘도 충분하다.

```swift
let id = user.userID
let id = user.identifier
```

함수나 메서드 이름을 지을 때도 마찬가지다.

```swift
struct ImageDownloader {
  func downloadImage(from url: URL) { ... }
}
```
`downloadImage(from:)`도 잘 지은 것 처럼 보이지만 실제로 사용해보면 image와 download라는 단어가 불필요하게 중복된다. 
```swift
let image = imageDownloader.downloadImage(from: url)
```

메서드가 사용될 때의 인스턴스 이름까지 고려해서 아래와 같이 중복을 제거해볼 수 있을 것이다.

```swift
let image = imageDownloader.fetch(from: url)
let image = imageManager.download(from: url)
```
등등.

## #6 스위프트의 getter

스위프트에서 어떤 인스턴스를 리턴하는 함수나 메서드에 `get`을 쓰지 않는다. `get` 없이 바로 타입 이름(명사)으로 시작하면 된다.

```swift
func date(from string: String) -> Date?
func anchor(for node: SCNNode) -> ARAnchor?                          
func distance(from location: CLLocation) -> CLLocationDistance        
func track(withTrackID trackID: CMPersistentTrackID) -> AVAssetTrack? 
```

## #7 `fetch`, `request`, `perform`

우리는 데이터를 어디선가 가져오는 함수를 자주 작성하게 된다. 디스크에 저장되어있는 이미지, 리모트 서버에 있는 유저 db, 메모리 캐싱되어 있는 데이터 등등. 그럴때 위와 비슷한 동사들을 사용한다. 가져오다, 요청하다, 수행하다 등 사전적인 의미는 크게 다르지 않지만 iOS 프레임워크들을 찬찬히 읽어보니 쓰이는 경우가 명확히 구분되어 있었다. 

### ✏️ 결과를 바로 리턴하는 `fetch`

```swift
//PHAsset - Photos Framework
class func fetchAssets(withLocalIdentifiers identifiers: [String], options: PHFetchOptions?) -> PHFetchResult<PHAsset>

//PHAssetCollection - Photos Framework
class func fetchAssets(in assetCollection: PHAssetCollection, options: PHFetchOptions?) -> PHFetchResult<PHAsset>

//NSManagedObjectContext - Core Data
func fetch<T>(_ request: NSFetchRequest<T>) throws -> [T] where T : NSFetchRequestResult
```

`fetch`를 쓴 함수는 결과물을 바로 리턴해준다. 오래 걸리지 않는 동기적 작업이라는 것이다. 결과가 0개인 경우를 제외하곤 요청이 실패하지 않는 종류의 작업이다. 강아지가 공을 물어오는 놀이를 영어로 "play fetch"라고 하는데, 공을 무조건 물어오는 강아지의 모습을 연상하며 사용하면 되지 않을까

<img src="/assets/posts/play-fetch.jpeg" width="300"/>  
(*playing fetch*)

### ✏️ 유저에게 요청하거나 작업이 실패할 수 있을 때 `request`

```swift
//PHImageManager
func requestImage(for asset: PHAsset, targetSize: CGSize, contentMode: PHImageContentMode, options: PHImageRequestOptions?, resultHandler: @escaping (UIImage?, [AnyHashable : Any]?) -> Void) -> PHImageRequestID

//PHAssetResourceManager
func requestData(for resource: PHAssetResource, options: PHAssetResourceRequestOptions?, dataReceivedHandler handler: @escaping (Data) -> Void, completionHandler: @escaping (Error?) -> Void) -> PHAssetResourceDataRequestID

//CLLocationManager
func requestAlwaysAuthorization()
func requestLocation()

//MLMediaLibrary
class func requestAuthorization(_ handler: @escaping (MPMediaLibraryAuthorizationStatus) -> Void)
```

반면에 `request`를 쓰는 함수들은 비동기 작업이어서 handler를 받고 있거나 delegate 콜백으로 결과를 전달해준다. 그리고 결과 타입이 옵셔널이고 실패했을 경우 실패 이유를 알수 있는 Error 객체도 있는걸로 보아 요청이 실패할 수도 있다는 걸 알 수 있다. 또한 유저에게 특정 권한을 요청하는 메서드는 모두 `request`로 시작한다. **이것을 보면 request는 실패할 수 있는 작업이거나 누군가가 요청을 거절 할 수도 있을 때 사용하면 좋을 것 같다.**

### ✏️ 작업의 단위가 클로져나 Request로 래핑 되어있으면 `perform` 혹은 `execute`

```swift
//VNImageRequestHandler
func perform(_ requests: [VNRequest]) throws

//PHAssetResourceManager
func performChanges(_ changeBlock: @escaping () -> Void, completionHandler: ((Bool, Error?) -> Void)? = nil)

//NSManagedObjectContext
func perform(_ block: @escaping () -> Void)

//CNContactStore
func execute(_ saveRequest: CNSaveRequest) throws

//NSFetchRequest
func execute() throws -> [ResultType]
```

`perform`이나 `execute`은 파라미터로 Request 객체나 클로져를 받는 경우이다.

## 덧붙이는 말

스위프트에 특화된 내용이기 때문에 다른 언어나 플랫폼은 또 다른 컨벤션을 가지고 있고 읽기 좋은 코드의 기준도 다르다. 예를 들어 자바에서는 `get`을 써서 getter를 만드는 것이 컨벤션이고, 또 안드로이드의 `View`는 `isHidden`이 아니라 `VISIBLE`, `INVISIBLE`, `GONE`을 쓴다. 이처럼 프로그래밍 언어에 따라, 플랫폼에 따라 달라진다.

## 마무리

최적의 알고리즘을 짜기 위해 고민하는 것 만큼이나 그 코드를 영어로 잘 표현하는 것도 중요한 일인 것 같다. 그러면 대체 어떤 단어를 쓰고 어떻게 이름 지어야 좋은 걸까? 먼저 [동의어 사전](https://www.thesaurus.com)을 활용해 다양한 단어를 찾아보고 뜻을 알아보자. 그리고, '[읽기 좋은 코드가 좋은 코드다(한빛미디어)](http://www.yes24.com/24/goods/6692314)'의 저자는 좋은 코드를 많이 읽어보라고 한다. 또 표준 라이브러리와 프레임워크 문서를 하루에 15분씩이라도 시간을 할애하여 읽어보면 좋다고 한다. 그 조언대로 스위프트와 iOS 공식 문서를 앞에서부터 순서대로 읽어봤는데 이 글에 요약한 것을 포함하여 많은걸 배울 수 있었다. 


