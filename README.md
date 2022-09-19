# 🍹쥬스 메이커

## 📖 목차
1. [소개](#-소개)
2. [Tree](#-Tree)
3. [고민한 점](#-고민한-점)
4. [타임라인](#-타임라인)
5. [실행 화면](#-실행-화면)
6. [트러블 슈팅](#-트러블-슈팅)
7. [참고 링크](#-참고-링크)

## 🌱 소개

`Dragon`과 `mene` 팀이 만든 쥬스 메이커 앱입니다.

- KeyWords

  - [UIKit]
      - `navigation bar`
      - `button`
      - `alert`
      - `stepper`
      - `Modality`
          - `instantiateViewController` / `performSegue`
      - `data observing`
          - `singleton pattern` / `KVO` / `notification center`
      - `IBOutlet`
          - `UILabel` / `UIButton` / `UIStepper`
              - `tag` / `weak`,`strong` reference
      - `Auto layout`
          - UIButton body size `compact` / `regular` 

## 🌲 Tree

```
JuiceMaker
├── Controller
│   ├── AppDelegate.swift
│   ├── ChangeInventoryViewController.swift
│   ├── HomeViewController.swift
│   └── SceneDelegate.swift
│   └── Info.plist
├── Model
│   ├── Fruit.swift
│   ├── FruitStore.swift
│   ├── Juice.swift
│   ├── JuiceMaker.swift
│   └── MakeFruitError.swift
└── View
    ├── Assets.xcassets
    │   ├── AccentColor.colorset
    │   │   └── Contents.json
    │   ├── AppIcon.appiconset
    │   │   └── Contents.json
    │   └── Contents.json
    └── Base.lproj
        ├── LaunchScreen.storyboard
        └── Main.storyboard
```
 
## 👀 고민한 점

### Step 1

- "FruitStore가 관리하는 과일의 종류 : 딸기, 바나나, 파인애플, 키위, 망고"를 `tuple`을 사용할지, `Dictionary`를 사용하여 구현할지 고민해 보았습니다.
  - Fruit 타입의 값과 재고 수량을 묶어서 사용할 수 있는 딕셔너리를 사용하는 것으로 결정하였습니다.

- FruitStore 초기화 시 초기재고 10개를 적용하는 방법에 대해서 고민했습니다.
  - 처음에는 `var stock: [Fruit : Int] = [.strawBerry : 10, .banana : 10, .pineApple : 10, .kiwi : 10, .mango : 10]` 처럼 딕셔너리에 초기값을 할당하는 방법을 생각해봤지만, 매직넘버를 사용하지 않는 방법을 고민하다`init(initialFruitCount: Int)`으로 파라미터를 받아 설정하도록 변경하였습니다.

- `Juice` enum에서 프로퍼티로 recipe를 받아오는 방법과 JuiceMaker 구조체 안에서 메서드로 recipe를 반환하는 방법에 대해 고민해보았습니다. enum에서 프로퍼티를 사용하는 방법을 공부해보고 익숙해지기 위해서 이번에는 enum의 프로퍼티를 사용해 보았습니다.

- 각 과일의 수량 n개를 변경하는 기능에 대해 Int값을 받아서 연산하는 함수를 생각해 보았으나, 스토리 보드상에 보여지는 재고 수정 화면이 `+`/`-`로 구현되어 있는 것을 확인하여 `addToInventory`와 `removeToInventory`으로 분리하여 구현하였습니다. 

- 현재 재고를 확인하는 방법으로 `checkStock`함수를 구현하였는데, 기능을 함수로 구분지어 놓고 사용할지 또는 딕셔너리에서 값을 그대로 갖고 와서 사용할지 고민해 보았습니다.

### Step 2

- Alert을 처음으로 구현해보면서 [HIG - Alert](https://developer.apple.com/design/human-interface-guidelines/components/presentation/alerts)을 먼저 읽고 협업을 시작하였습니다.

- `재고수정` 버튼 클릭 시 화면이동 방법에 대해 고민해보았습니다.
    - 처음 받았던 프로젝트 파일에는 네비게이션 콘트롤러가 2개로 구현되어 있었는데 쥬스주문 화면에서 `재고수정`이라는 새로운 context를 만드는 것이라 생각하고 `Modal`을 사용하였습니다.
    - 이동한 재고수정 view에서 쥬스주문화면으로 돌아올 방법이 없어서 `Close`버튼을 구현하였습니다.

- 요구사항에서 "재고가 없는 경우 `예` 선택시 재고수정화면으로 이동 / `아니오` 선택시 얼럿닫기"로 기술되어 있었는데 HIG에서 "you can use “OK” for acceptance, avoiding “Yes” and “No.” Always use “Cancel” to title a button that cancels the alert’s action."라는 부분을 확인하였습니다. 
    - 현업이었다면 기획자와 상의하고 요구사항을 수정하는 것이 맞겠지만 HIG를 따르는 것으로 결정하고 `예` / `아니오` 대신 `취소` / `재고수정` 으로 변경하여 구현하였습니다.

### Step 3

- 뷰 컨트롤러 사이에서 과일 재고 데이터를 전달하는 방식에 대해 여러 방법을 생각해 보았습니다. 
    - 처음에는 juiceMaker 파일의 `store`를 static으로 설정하여 사용하였습니다.
    - 리팩토링하면서 FruitStore 클라스에 `singleton pattern`을 적용하였습니다.

- `HomeViewController`에서 인벤토리에 있는 값을 가져오는 방법에 대해 고민해보았습니다.
    - UIViewController View State Method에서 `ViewWillAppear`함수를 사용하여 뷰가 올라올 때마다 데이터를 가져올 수 있도록 구현하였습니다.
        - ViewWillAppear함수 활용을 위해 `presentChangeInventoryViewController`함수에서 `changeInventoryVC.modalPresentationStyle = .fullScreen`으로 설정해주었습니다.

- `ChangeInventoryViewController`에서 Stepper의 value를 Stepper를 누르는 순간 하나의 함수에서 값을 가져오고 레이블을 업데이트하고 재고에 반영하는 일련의 동작을 하도록 구현하려고 시도해보았습니다.
    - `ViewDidLoad` 함수에서 `ChangeInventoryViewController`로딩이 완료되면  Stepper의 Value를 가져오는 방식으로 최종 구현하였습니다.

- 각각의 view elements의 사용방법에 대해 고민해 보았습니다.
    - `tag`를 사용하는 방법, `IBOutlet`을 선언하지 않고 코드단에서 처리하는 방법 등을 적용해 보았습니다.

- Auto Layout을 적용하기 위해 공부해 보았습니다.
    - 각각의 요소에 제약을 다 적용하기는 어려울 것 같아서 `Vertical Stack View`를 사용하여 과일/갯수 레이블 / 스태퍼를 묶어주고, 5개의 과일 묶음을 `Horizontal Stack View`를 이용하여 다시 묶어주었습니다.
    - `HomeViewController` 화면에서 기기 사이즈가 작은 경우 버튼의 텍스트가 "파인..주문"으로 노출되는 문제를 해결하기 위해 화면크기에 따라 폰트사이즈가 조절되도록 구현하였습니다.
        - 처음 생각한 방법: 버튼의 텍스트를 2줄로 줄바꿈하여 보여주면 ...으로 생략되지 않을 것 같아 버튼 title을 `Plain`에서 `Attributed`로 변경하고 줄바꿈해 보았지만 버튼 텍스트에 첫번째 줄만 노출되는 문제가 있었습니다
        - 해결방안: 폰트에서 Body로 `Compact` & `Regular` 각각의 사이즈를 주어 작은 화면에서도 폰트가 "..."으로 생략되지 않도록 구현하였습니다.
    
## ⏰ 타임라인

<details>
<summary>Step 1 타임라인</summary>
    
- **220830**
    - Enum `Fruit`파일 추가
    - `FruitStore` 클래스에서 `addToStore`메서드 추가
    - `FruitStore` 클래스에서 `removeToStore`메서드 추가
    - Enum `Juice`파일 추가하고 recipe 프로퍼티에서 필요한 과일 딕셔너리 값 저장하도록 구현
    - `JucieMaker` 구조체에서 `checkRecipe` 메서드 추가
    - `Juice` enum에 String rawValue 추가하여 쥬스 이름으로 사용하도록 구현
    - `JuiceMaker` 구조체에서 `makeJuice` 메서드 구현
    - `JuiceMaker` 구조체에서 과일의 현재 재고를 출력하는 `checkStock`함수 추가
    
- **220831**
    - Step1 PR 리뷰 요청
        - [PR보러가기](https://github.com/yagom-academy/ios-juice-maker/pull/255)

</details>
    
<details>
<summary>Step 2 타임라인</summary>   
    
### Step 2

- **220902**
    - 과일 재고를 변경할 수 있는 `ChangeInventoryViewController` 생성 및 Modality화면전환 구현
    - 과일주문버튼 클릭시 Alert 동작 구현
    
- **220906**
    - 쥬스 주문 버튼 클릭시 성공/실패 여부를 알려주는 얼럿 생성하는 `showAlert`함수 구현
    - Inventory에 있는 fruit 갯수를 확인하는 `checkInventory`함수 추가
    - 재고가 부족할 경우 노출되는 얼럿의 `재고수정` 클릭시 재고수정 모달로 이동하는 `presentChangeInventoryViewController` 함수 구현
    - `makeJuice`함수에서 과일 두개가 필요한 경우 첫번째 과일은 충분하고 두번째 과일이 부족할 때 첫번째 과일이 차감된 후 쥬스생성 실패하는 얼럿이 뜨는 에러 수정
    - 접근제어 설정 및 코딩 컨벤션 통일
    - Step2 PR 리뷰 요청
        - [PR보러가기](https://github.com/yagom-academy/ios-juice-maker/pull/261)
    
- **220908**
    - review 적용하여 리팩토링
        - `checkInventory`함수에서 stockLable값 옵셔널 바인딩하지 않고 직접 가져오도록 수정
        - `showAlert`함수 `showSuccessAlert`과 `showFailAlert`으로 분리
        - 각각존재했던 쥬스주문 버튼 함수를`orderFruitJuice`하나의 함수에서 처리하도록 변경
    
</details>
 
<details>
<summary>Step 3 타임라인</summary>   
    
### Step 3

- **220912**
    - 얼럿만드는 showAlert함수 `showSuccessAlert`과 `showFailAlert`으로 분리
    - 각각의 ActionButton을 통합하여 하나의 `orderFruitJuice` 함수에서 처리하도록 리팩토링
        - UIButton을 Juice타입으로 변환해주는 `takeJuiceMenu`함수를 생성하여 내부에서 사용하도록 구현
    - ChangeInventoryVC에서 재고 업데이트 할 수 있도록 JuiceMaker 구조체의 `store` static으로 변경
    - ChangeInventoryVC에서 `takeFruitLabel`함수 생성
    - Stepper ChangeInventoryViewController에 연결 후 동작 구현
    - Stepper 초기값을 지정하는 `checkStepperValue` 함수 구현
    - ChangeInventoryViewController가 dismiss될 때 최종 Label값을 인벤토리에 담아주는 `updateInventory` 함수 구현
    - 재고추가화면 `제목`과 `닫기` 버튼 구현를 위한 NavigationController 추가
    - ChangeInventoryVC에 `StackView` & `AutoLayout` 적용
    - Stepper를 매개변수로 받아 과일 타입을 반환하는 `takeFruit` 함수 구현
    - `updateInventory` 함수 삭제하고 `setFruitStock` 함수에서 인벤토리 값 업데이트 하도록 수정
    - HomeViewController에 기기별 버튼 텍스트 크기 조정을 위한 `compact`, `regular` 폰트사이즈 적용
    - ChangeInventoryVC Auto Layout개선을 위해 `alignment`, `spacing` 조절
    - Step3 PR 리뷰 요청
        - [PR보러가기](https://github.com/yagom-academy/ios-juice-maker/pull/273)

- **220914**
    - `ChangeInventoryViewController` 코드 은닉화 적용
    - review 적용하여 리팩토링
        - FruitStore `Singleton Disign pattern` 적용
    - 얼럿 생성하는 2개의 함수 `showSuccessAlert`과 `showFailAlert`를 title과 message를 매개변수로 받는 하나의 함수 `makeAlert`으로 리팩토링
    - `ChangeInventoryVC`에서 Stepper를 IBOutlet을 사용하지 않고 `tag`를 사용하도록 리팩토링
    
 </details>
    
## 📱 실행 화면

|iPhone 12 Pro Max|
|:--:|
|![](https://i.imgur.com/DfWpxSc.gif)|

|iPod touch(7th gen)|
|:--:|
|![](https://i.imgur.com/N2JbTc4.gif)|

## ❓ 트러블 슈팅

### Step 1

- 쥬스를 만드는데 필요한 과일 개수(`recipe`)를 받아오는 방법을 고민했습니다.

  - 기존코드 (함수로 Return하여 사용)

  ```swift
     func checkRecipe(of juice: Juice) -> [Fruit: Int] {
        switch juice {
        case .strawBerry:
            return [.strawBerry: 16]
        case .banana:
            return [.banana: 2]
        case .pineApple:
            return [.pineApple: 2]
        case .kiwi:
            return [.kiwi: 3]
        case .mango:
            return [.mango: 3]
        case .strawBerryBanana:
            return [.strawBerry: 10, .banana: 1]
        case .mangoKiwi:
            return [.mango: 2, .kiwi: 1]
        }
    }
  
  let recipe = checkRecipe(of: juice)
          
  ```

  - 변경코드 (Enum-property 사용)

  ```swift
  enum Juice: String {
    case strawBerry = "딸기"
    case banana = "바나나"
    case pineApple = "파인애플"
    case kiwi = "키위"
    case mango = "망고"
    case strawBerryBanana = "딸바"
    case mangoKiwi = "망고키위"

    var recipe: [Fruit: Int] {
        switch self {
        case .strawBerry:
            return [.strawBerry: 16]
        case .banana:
            return [.banana: 2]
        case .pineApple:
            return [.pineApple: 2]
        case .kiwi:
            return [.kiwi: 3]
        case .mango:
            return [.mango: 3]
        case .strawBerryBanana:
            return [.strawBerry: 10, .banana: 1]
        case .mangoKiwi:
            return [.mango: 2, .kiwi: 1]
        }
     }
  }
  
  let recipe = juice.recipe
  ```

### Step 2

- `JuiceMaker.swift`파일의 makeJuice 함수에서 쥬스 레시피가 2개일 때, 첫번째 과일이 재고가 있고 2번째 과일의 재고가 부족한 케이스에서 for문 안에서 첫번째 과일 재고가 차감되고 그 후에 "재고가 부족합니다"가 뜨는 문제가 있었습니다.

    - 해결방안: for문을 2번 돌려 첫번째 for문에서 이상 있을 경우 false로 반환, 이상 없을 경우 다시 for문을 통해 inventory에 값을 적용시켜주는 방식으로 변경하였습니다.

- 각각의 `@IBAction` 버튼에 할당되어 있던 중복코드를 하나로 만들어 보기 위해 고민해보았습니다.

```swift
@IBAction private func orderStrawberryJuice(_ sender: UIButton) {
        showAlert(of: .strawBerry)
        checkInventory()
    }

  ...

@IBAction private func orderMangoKiwi(_ sender: UIButton) {
        showAlert(of: .mangoKiwi)
        checkInventory()
    }
```

첫번째 생각한 방법은 Juice.swift enum의 rawValue를 Int로 변경하고 각각의 쥬스 주문 버튼에 tag를 설정하여 Juice(rawValue: sender.tag)로 쥬스타입을 불러와서 적용하는 방법이었습니다.

```swift
@IBAction private func orderFruitJuice(_ sender: UIButton) {
        guard let juice = Juice(rawValue: sender.tag) else { return }
         
        if juiceMaker.makeJuice(of: juice) {
            showSuccessAlert(of: juice)
            checkInventory()
        } else {
            showFailAlert()
        }
    }

enum Juice: Int {
    case strawBerry = 1
    case banana = 2
    case pineApple = 3
    case kiwi = 4
    case mango = 5
    case strawBerryBanana = 6
    case mangoKiwi = 7
    
    var name: String {
        switch self {
        case .strawBerry:
            return "딸기"
        case .banana:
            return "바나나"
        case .pineApple:
            return "파인애플"
        case .kiwi:
            return "키위"
        case .mango:
            return "망고"
        case .strawBerryBanana:
            return "딸바"
        case .mangoKiwi:
            return "망고키위"
        }
    }
}
```
이렇게 적용할 경우 버튼의 tag를 Int 타입으로만 설정할 수 있어 각각의 버튼의 몇번의 tag를 갖는지 직관적으로 알 수 없다는 단점이 있었습니다. 매직넘버가 되어 추후에 새로운 쥬스가 추가되거나 유지보수가 어려울 것 같아 다른 방법을 생각해 보았습니다. (enum의 rawValue가 Int로 변경됨에 따라 추가적으로 name이라는 연산프로퍼티도 생성해 줘야해서 불편했습니다.)

다시 생각해 본 방법은 sender로 받은 버튼을 juice 타입으로 변환해주는 takeJuiceMenu함수를 생성하여 Juice 타입으로 반환하고 그 값을 makeJuice함수의 파라미터로 사용하였습니다.

```swift
@IBAction private func orderFruitJuice(_ sender: UIButton) {
       guard let juice = takeJuiceMenu(of: sender) else { return }
        
        if juiceMaker.makeJuice(of: juice) {
            showSuccessAlert(of: juice)
            checkInventory()
        } else {
            showFailAlert()
        }
    }

 func takeJuiceMenu(of sender: UIButton) -> Juice? {
        switch sender {
        case strawberryBananaJuiceOrderButton:
            return .strawBerryBanana
        case mangoKiwiJuiceOrderButton:
            return .mangoKiwi
        case strawberryJuiceOrderButton:
            return .strawBerry
        case bananaJuiceOrderButton:
            return .banana
        case pineappleJuiceOrderButton:
            return .pineApple
        case kiwiJuiceJuiceOrderButton:
            return .kiwi
        case mangoJuiceOrderButton:
            return .mango
        default:
            return nil
        }
    }
```

### Step 3

- Stepper의 @IBOutlet을 사용하지 않고 코드를 간소화하기 위해 `tag`를 사용하여 리팩토링하였습니다.
    - @IBAction에서 Switch문을 사용하여 tag 정수값에 따라 각 과일의 값 변경
    - tag로 설정한 Stepper의 갯수를 `setFruitStepper`함수의 매개변수로 입력하면 UIView에서 viewWithTag기능을 사용하여 UIStpper로 다운캐스팅을 하여 Stepper값을 배열에 저장
    - `setFruitSteppr`함수를 통해 배열에 저장된 UIStepper에 for문을 통해 inventory의 fruit값을 Stepper의 초기값으로 설정
    
    ```swift
    @IBAction private func setFruitStock(_ sender: UIStepper) {
        switch sender.tag {
        case 1:
            strawberryStockLabel.text = Int(sender.value).description
            juiceMaker.store.inventory[.strawberry] = Int(sender.value)
        case 2:
            bananaStockLabel.text = Int(sender.value).description
            juiceMaker.store.inventory[.banana] = Int(sender.value)
        case 3:
            pineappleStockLabel.text = Int(sender.value).description
            juiceMaker.store.inventory[.pineapple] = Int(sender.value)
        case 4:
            kiwiStockLabel.text = Int(sender.value).description
            juiceMaker.store.inventory[.kiwi] = Int(sender.value)
        case 5:
            mangoStockLabel.text = Int(sender.value).description
            juiceMaker.store.inventory[.mango] = Int(sender.value)
        default:
            break
        }
    }
    
    private func setFruitStepper(number stepper: Int) -> [UIStepper] {
        var fruitStepperArray: [UIStepper] = []
        
        for tagNumber in 1...stepper {
            guard let fruitStepper = self.view.viewWithTag(tagNumber) as? UIStepper
            else { break }
            fruitStepperArray.append(fruitStepper)
        }
        
        return fruitStepperArray
    }
    
    private func checkStepperValue() {
        var fruitStepper: [UIStepper] = setFruitStepper(number: 5)
        
        for fruit in Fruit.allCases {
            let stepper = fruitStepper.removeFirst()
            stepper.value = Double(juiceMaker.store.inventory[fruit] ?? 0)
        }
    }
    ```
    
    tag를 사용해보니, 무분별한 tag는 매직넘버처럼 사용되어 오히려 코드의 직관성을 많이 떨어뜨린다고 생각이 되었고 UIView elements 중 한 종류에만 tag를 설정해 주어야 그나마 유용한 tag의 활용이라고 생각되었습니다.
만약, UILabel과 UIStepper 모두를 tag로 사용하면, tag번호끼리도 구분을 줘야하고 갯수가 많아짐으로써 어떤 넘버가 정수값에 따른 UIView element인지 구분이 어려워지기 때문입니다.

- `showSuccessAlert`과 `showFailAlert`로 분리되어 있던 얼럿 생성을 하나의 함수로 구현하기 위해 `makeAlert` 함수로 리팩토링하였습니다.
    - 매개변수 `checkSuccess`에서 Bool값에 따라 추가 UIAlertAction이 생성되도록 구현
    - 얼럿에서 title과 메세지등 여러개의 String이 사용되어 별도의 nameSpace를 추가하고 호출하는 방법도 생각해 보았으나, 유지보수 측면에서는 좋아지지만 코드의 가독성이 떨어진다고 생각되어 구현하지는 않았습니다. 추후 기획이 변경될 가능성이 있는 프로젝트라면 nameSpace를 활용하는 편이 Human Error를 방지할 수 있어 좋을 수 있을 것 같습니다.

    ```swift
    private func makeAlert(title: String, message: String, checkSuccess: Bool) {
        let alert = UIAlertController(title: title,
                                      message: message,
                                      preferredStyle: .alert)
        let ok = UIAlertAction(title: "확인",
                               style: .default,
                               handler: {_ in
            if !checkSuccess {
                self.presentChangeInventoryViewController()
            }
        })
        if !checkSuccess {
            let cancel = UIAlertAction(title: "취소",
                                       style: .cancel,
                                       handler: nil)
            
            alert.addAction(cancel)
        }
        
        alert.addAction(ok)
        present(alert, animated: true, completion: nil)
    }
    ```

## 🔗 참고 링크

[Swift API Design Guidelines - Naming](https://swift.org/documentation/api-design-guidelines/)  
[Swift Language Guide - Initialization](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)  
[Swift Language Guide - Access Control](https://docs.swift.org/swift-book/LanguageGuide/AccessControl.html)  
[Swift Language Guide - Nested Types](https://docs.swift.org/swift-book/LanguageGuide/NestedTypes.html)   
[Human Interface Guidelines - Alerts](https://developer.apple.com/design/human-interface-guidelines/components/presentation/alerts/)  
[WWDC2015 - Implementing UI Designs in Interface Builder](https://developer.apple.com/videos/play/wwdc2015/407/)  
[Should Outlets Be Weak or Strong
](https://cocoacasts.com/should-outlets-be-weak-or-strong)[Should IBOutlet be weak or strong var?](https://stackoverflow.com/questions/29421614/should-iboutlet-be-weak-or-strong-var)  
[Documentation Archive - Outlets](https://developer.apple.com/library/archive/documentation/General/Conceptual/Devpedia-CocoaApp/Outlet.html)  
[viewWithTag](https://developer.apple.com/documentation/uikit/uiview/1622429-viewwithtag)  
[야곰닷넷 - 오토레이아웃 정복하기](https://yagom.net/courses/autolayout/)

---

[🔝 맨 위로 이동하기](#쥬스-메이커)
