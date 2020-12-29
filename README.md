# Swift extension Collection 

유용한 swift extension 모아보기! 더 좋은 방법, 코드를 위해 많은 조언과 지적. 감사드립니다.
- [String](#string)
    - #### Swift
    - [문자열 인덱스](#문자열-인덱스)
    - [문자열 인덱싱](#문자열-인덱싱)
    - [문자열 자르기](#문자열-자르기)
    - [진법변환](#진법변환)
    - [특수문자 확인](#특수문자-확인)
    
    - #### iOS
    - [addLineSpacing](#addlinespacing)
    - [boundingRect](#boundingrect)
    - [hexToUIColor](#hextouicolor)

- [Int](#int)
    - [거듭제곱](#거듭제곱)
    - [랜덤](#랜덤)
    - [거듭제곱](#거듭제곱)
    - [최대공약수와 최소공배수](#최대공약수와-최소공배수)
    - [소수](#소수)
    - [BinaryInteger](#binaryinteger)
    
- [Date](#date)

- [UIColor](#uicolor)
    - [hexString](#hexstring)
    
- [UIView](#uiview)
    - [addLine](#addline)
    - [addBottomBorderWithColor](#addbottomborderwithcolor)
    - [anchor](#anchor)
    - [bindToKeyboard](#bindtokeyboard)
    - [corner](#corner)
    - [gradient](#gradient)
    - [Infinite Rotate](#infinite-rotate)
    - [shake](#shake)
    - [shadow](#shadow)
    - [specific corners](#specific-corners)

- [UIImageView](#uiimageview)
    - [load(urlString:)](#loadurlstring)
    - [setImageColor](#setimagecolor)
    
- [UIImage](#uiimage)
    - [scaled](#scaled)

- [UITextView](#uitextview)
    - [numberOfLine](#numberofline)
    - [centerVertically](#centervertically)

- [UINavigationController](#uinavigationcontroller)
    - [pushVC](#pushvc)

## String

### 문자열 인덱스
```swift
extension String {
    subscript(i: Int) -> Character {
        var offset = i
        while offset < 0 { offset += count }
        let index = self.index(startIndex, offsetBy: offset)
        return self[index]
    }
}

let hello = "hello"
let world = "world"
let greeting = hello + world

greeting[-1] // "d"
greeting[1] // "e"
```

### 문자열 인덱싱
```swift
extension String {
    subscript(r: Range<Int>) -> String {
        get {
            let startIndex = self.index(self.startIndex, offsetBy: r.lowerBound)
            let endIndex = self.index(self.startIndex, offsetBy: r.upperBound)
            
            return String(self[startIndex..<endIndex])
        }
    }
    
    subscript(r: ClosedRange<Int>) -> String {
        get {
            let startIndex = self.index(self.startIndex, offsetBy: r.lowerBound)
            let endIndex = self.index(self.startIndex, offsetBy: r.upperBound)
            
            return String(self[startIndex...endIndex])
        }
    }
}

var str = "Hi woongs"
print(str[0..<2]) // "Hi"
print(str[3...8]) // "woongs"
```

### 문자열 자르기
```swift
extension String {
    func split(by length: Int) -> [String] {
        var resultString = [String]()
        
        var string = self
        
        while string.count >= length {
            let range = string.startIndex..<string.index(string.startIndex, offsetBy: length)
            resultString.append(String(string[range]))
            string.removeFirst(length)
        }
        if !string.isEmpty { resultString.append(string) }
        
        return resultString
    }
}

let target = "abcabcd"

let truncatedString = target.split(by: 3)
print(truncatedString)
// ["abc", "abc", "d"]
```


### 진법변환
_[거듭제곱](#거듭제곱) 활용_

현재 진법에서 주어진 진법으로 변환해주는 extension. 단, 2 ~ 36진법까지 가능하며 10부터는 A ~ Z로 매칭한다.

```swift
extension Int {
    func square(_ num: Int) -> Int {
        var squaredNumber = 1
        for _ in 0..<num {
            squaredNumber *= self
        }
        return squaredNumber
    }
}

extension String {
    // 2 ~ 36 진법
    func numberSystemConvert(from c: Int, to p: Int) -> String {
        let matchingList = Array("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ")
        var n = Array(self)
        var decimal = 0
        var resultArr: [Character] = []
        var result = ""
        
        // get decimal
        for i in 0..<n.count {
            if let idx = matchingList.firstIndex(of: n[n.count - 1 - i]) {
                let value = idx
                if value >= c {
                    return "Check your value. value more than `from` number system."
                }
                decimal += value * c.square(i)
            } else {
                return "Can't matching character"
            }
        }
        
        if decimal == 0 {
            return "0"
        }
        
        // convert p number system
        while decimal > 0 {
            let idx = decimal % p
            decimal /= p
            resultArr.append(matchingList[idx])
        }
        // make return value
        for i in 0..<resultArr.count {
            result += String(resultArr[resultArr.count - 1 - i])
        }
        
        return result
    }
}
```

### 특수문자 확인

```swift
func hasSpecialCharacters() -> Bool {

    do {
        let regex = try NSRegularExpression(pattern: ".*[^A-Za-z0-9].*", options: .caseInsensitive)
        if let _ = regex.firstMatch(in: self, options: NSRegularExpression.MatchingOptions.reportCompletion, range: NSMakeRange(0, self.count)) {
            return true
        }

    } catch {
        debugPrint(error.localizedDescription)
        return false
    }

    return false
}
```


### addLineSpacing
```swift
extension String {
    func addLineSpacing(spacing: CGFloat) -> NSMutableAttributedString {
        let attributedString = NSMutableAttributedString(string: self)
        
        let paragraphStyle = NSMutableParagraphStyle()
        paragraphStyle.lineSpacing = spacing
        
        attributedString.addAttribute(NSAttributedString.Key.paragraphStyle, value: paragraphStyle, range: NSMakeRange(0, attributedString.length))
        return attributedString
    }
}

...

label.attributedText = text.addLineSpacing(spacing: 2)
```

### boundingRect

```swift
import UIKit

extension String {

  func boundingRect(with size: CGSize, attributes: [NSAttributedString.Key: Any]) -> CGRect {
    let options: NSStringDrawingOptions = [.usesLineFragmentOrigin, .usesFontLeading]
    let rect = self.boundingRect(with: size, options: options, attributes: attributes, context: nil)
    return snap(rect)
  }

  func size(fits size: CGSize, font: UIFont, maximumNumberOfLines: Int = 0) -> CGSize {
    let attributes: [NSAttributedString.Key: Any] = [.font: font]
    var size = self.boundingRect(with: size, attributes: attributes).size
    if maximumNumberOfLines > 0 {
      size.height = min(size.height, CGFloat(maximumNumberOfLines) * font.lineHeight)
    }
    return size
  }

  func width(with font: UIFont, maximumNumberOfLines: Int = 0) -> CGFloat {
    let size = CGSize(width: CGFloat.greatestFiniteMagnitude, height: CGFloat.greatestFiniteMagnitude)
    return self.size(fits: size, font: font, maximumNumberOfLines: maximumNumberOfLines).width
  }

  func height(fits width: CGFloat, font: UIFont, maximumNumberOfLines: Int = 0) -> CGFloat {
    let size = CGSize(width: width, height: CGFloat.greatestFiniteMagnitude)
    return self.size(fits: size, font: font, maximumNumberOfLines: maximumNumberOfLines).height
  }
}
```

### hexToUIColor
```swift
extension String {
    func hexToColor(alpha:CGFloat = 1.0) -> UIColor {
        var cString:String = self.trimmingCharacters(in: .whitespacesAndNewlines).uppercased()

        if (cString.hasPrefix("#")) {
            cString.remove(at: cString.startIndex)
        }

        if ((cString.count) != 6) {
            return UIColor.gray
        }

        var rgbValue: UInt64 = 0
        Scanner(string: cString).scanHexInt64(&rgbValue)

        return UIColor(
            red: CGFloat((rgbValue & 0xFF0000) >> 16) / 255.0,
            green: CGFloat((rgbValue & 0x00FF00) >> 8) / 255.0,
            blue: CGFloat(rgbValue & 0x0000FF) / 255.0,
            alpha: alpha
        )
    }
}
```

## Int

### 거듭제곱
```swift
extension Int {
    func square(_ num: Int) -> Int {
        var squaredNumber = 1
        for _ in 0..<num {
            squaredNumber *= self
        }
        return squaredNumber
    }
}
```

### 랜덤  
해당 정수 범위 안에서 랜덤 값을 리턴한다.
```swift
extension Int {
  var arc4random: Int {
    if self > 0 {
      return Int(arc4random_uniform(UInt32(self)))
    } else if self < 0 {
      return -Int(arc4random_uniform(UInt32(abs(self))))
    } else {
      return 0
    }
  }
}
...
print(5.arc4random) // 0 ~ 4 사이의 랜덤값 
```

### 최대공약수와 최소공배수

``` swift
extension Int {
    func gcd(with n: Int) -> Int {
        return (self % n == 0) ? n : n.gcd(with: self%n)
    }
    
    func lcm(with n: Int) -> Int {
        return (self * n) / self.gcd(with: n)
    }
}
```

### 소수
```swift
extension Int {
    var isPrime: Bool {
        guard self != 1 else { return false }
        guard self != 2, self != 3 else { return true }
        for i in 2...Int(sqrt(Double(self))) {
            if self%i == 0 {
                return false
            }
        }
        return true
    }
}
```

### BinaryInteger
```swift
extension BinaryInteger {
    var binaryDescription: String {
        var binaryString = ""
        var internalNumber = self
        var counter = 0

        for _ in (1...self.bitWidth) {
            binaryString.insert(contentsOf: "\(internalNumber & 1)", at: binaryString.startIndex)
            internalNumber >>= 1
            counter += 1
            if counter % 4 == 0 {
                binaryString.insert(contentsOf: " ", at: binaryString.startIndex)
            }
        }

        return binaryString
    }
}

UInt8(9).binaryDescription      // "0000 1001"
Int8(5).binaryDescription       // "0000 0101"
UInt16(1945).binaryDescription  // "0000 0111 1001 1001"

Int16(14).binaryDescription     // "0000 0000 0000 1110"
Int32(6).binaryDescription      // "0000 0000 0000 0000 0000 0000 0000 0110"
UInt32(2018).binaryDescription  // "0000 0000 0000 0000 0000 0111 1110 0010"
```


## Date
```swift
extension Date {
    static var yesterday: Date { return Date().dayBefore }
    static var tomorrow:  Date { return Date().dayAfter }
    var dayBefore: Date {
        return Calendar.current.date(byAdding: .day, value: -1, to: noon)!
    }
    var dayAfter: Date {
        return Calendar.current.date(byAdding: .day, value: 1, to: noon)!
    }
    var noon: Date {
        return Calendar.current.date(bySettingHour: 12, minute: 0, second: 0, of: self)!
    }
    var month: Int {
        return Calendar.current.component(.month,  from: self)
    }
    var isLastDayOfMonth: Bool {
        return dayAfter.month != month
    }
    // 1 = 일요일
    var weekday: Int {
        return Calendar.current.component(.weekday, from: self)
    }
    var firstDayOfTheMonth: Date {
        return Calendar.current.date(from: Calendar.current.dateComponents([.year, .month], from: self))!
    }
}
```

## UIColor

### hexString
[참고](https://stackoverflow.com/questions/24263007/how-to-use-hex-color-values)  

```swift
extension UIColor {
    convenience init(hexString: String) {
        let hex = hexString.trimmingCharacters(in: CharacterSet.alphanumerics.inverted)
        var int = UInt64()
        Scanner(string: hex).scanHexInt64(&int)
        let a, r, g, b: UInt64
        switch hex.count {
        case 3: // RGB (12-bit)
            (a, r, g, b) = (255, (int >> 8) * 17, (int >> 4 & 0xF) * 17, (int & 0xF) * 17)
        case 6: // RGB (24-bit)
            (a, r, g, b) = (255, int >> 16, int >> 8 & 0xFF, int & 0xFF)
        case 8: // ARGB (32-bit)
            (a, r, g, b) = (int >> 24, int >> 16 & 0xFF, int >> 8 & 0xFF, int & 0xFF)
        default:
            (a, r, g, b) = (255, 0, 0, 0)
        }
        self.init(red: CGFloat(r) / 255, green: CGFloat(g) / 255, blue: CGFloat(b) / 255, alpha: CGFloat(a) / 255)
    }
}
```

## UIView

### addLine

밑줄 긋기 

```swift
enum LINE_POSITION {
    case top
    case bottom
}

extension UIView {
    func addLine(position : LINE_POSITION, color: UIColor, width: Double) {
        let lineView = UIView()
        lineView.backgroundColor = color
        lineView.translatesAutoresizingMaskIntoConstraints = false // This is important!
        self.addSubview(lineView)

        let metrics = ["width" : NSNumber(value: width)]
        let views = ["lineView" : lineView]
        self.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "H:|[lineView]|", options:NSLayoutConstraint.FormatOptions(rawValue: 0), metrics:metrics, views:views))

        switch position {
        case .top:
            self.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "V:|[lineView(width)]", options:NSLayoutConstraint.FormatOptions(rawValue: 0), metrics:metrics, views:views))
            break
        case .bottom:
            self.addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "V:[lineView(width)]|", options:NSLayoutConstraint.FormatOptions(rawValue: 0), metrics:metrics, views:views))
            break
        }
    }
}
```

### addBottomBorderWithColor
```swift
extension UIView {
    func addBottomBorderWithColor(color: UIColor, width: CGFloat) {
        let border = UIView()
        border.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        border.frame = CGRect(x: self.frame.origin.x,
                              y: self.frame.origin.y+self.frame.height-width, width: self.frame.width, height: width)
        border.backgroundColor = color
        self.superview!.insertSubview(border, aboveSubview: self)
    }
}
```

### anchor
```swift
extension UIView {
    func anchor(top: NSLayoutYAxisAnchor?, leading: NSLayoutXAxisAnchor?, bottom: NSLayoutYAxisAnchor?, trailing: NSLayoutXAxisAnchor?, padding: UIEdgeInsets = .zero, size: CGSize = .zero) {
        translatesAutoresizingMaskIntoConstraints = false
        
        if let top = top {
            topAnchor.constraint(equalTo: top, constant: padding.top).isActive = true
        }
        
        if let leading = leading {
            leadingAnchor.constraint(equalTo: leading, constant: padding.left).isActive = true
        }
        
        if let bottom = bottom {
            bottomAnchor.constraint(equalTo: bottom, constant: -padding.bottom).isActive = true
        }
        
        if let trailing = trailing {
            trailingAnchor.constraint(equalTo: trailing, constant: -padding.right).isActive = true
        }
        
        
        if size.width != 0 {
            widthAnchor.constraint(equalToConstant: size.width).isActive = true
        }
        
        if size.height != 0 {
            heightAnchor.constraint(equalToConstant: size.height).isActive = true
        }
    
    }
}

```


### bindToKeyboard

keyboard가 나타나고 사라짐에 따라 뷰 조정하기

```swift
extension UIView {
    func bindToKeyboard() {
        NotificationCenter.default.addObserver(self, selector: #selector(keyboardWillChange(_:)), name: UIResponder.keyboardWillChangeFrameNotification, object: nil)
    }
    
    @objc private func keyboardWillChange(_ notification: NSNotification) {
        let duration = notification.userInfo?[UIResponder.keyboardAnimationDurationUserInfoKey] as! Double
        let curve = notification.userInfo?[UIResponder.keyboardAnimationCurveUserInfoKey] as! UInt
        let begginingFrame = (notification.userInfo?[UIResponder.keyboardFrameBeginUserInfoKey] as! NSValue).cgRectValue
        let endFrame = (notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as! NSValue).cgRectValue
        let deltaY = endFrame.origin.y - begginingFrame.origin.y
        
        UIView.animateKeyframes(withDuration: duration, delay: 0.0, options: UIView.KeyframeAnimationOptions(rawValue: curve), animations: {
            self.frame.origin.y += deltaY
        }, completion: nil)
    }
}
```

### corner
```swift
extension UIView {
    func corners(_ radius: CGFloat) -> UIView {
        self.layer.cornerRadius = radius

        return self
    }
}
```

### gradient
```swift
func addGradient(colors: [CGColor], locations: [NSNumber]?) {
    let gradient = CAGradientLayer()
    gradient.frame = self.frame
    gradient.colors = colors
    gradient.locations = locations

    self.layer.addSublayer(gradient)
}
```

### Infinite Rotate
custom indicator를 만들 때 유용하다.

```swift
extension UIView {
    private static let kRotationAnimationKey = "rotationanimationkey"

    func rotate(duration: Double = 1) {
        if layer.animation(forKey: UIView.kRotationAnimationKey) == nil {
            let rotationAnimation = CABasicAnimation(keyPath: "transform.rotation")

            rotationAnimation.fromValue = 0.0
            rotationAnimation.toValue = Float.pi * 2.0
            rotationAnimation.duration = duration
            rotationAnimation.repeatCount = Float.infinity

            layer.add(rotationAnimation, forKey: UIView.kRotationAnimationKey)
        }
    }

    func stopRotating() {
        if layer.animation(forKey: UIView.kRotationAnimationKey) != nil {
            layer.removeAnimation(forKey: UIView.kRotationAnimationKey)
        }
    }
}
```

### shadow
```swift
extension UIView {
    func shadow(radius: CGFloat, color: UIColor, offset: CGSize, opacity: Float) {
        self.layer.masksToBounds = false
        self.layer.shadowRadius = radius
        self.layer.shadowColor = color.cgColor
        self.layer.shadowOffset = offset
        self.layer.shadowOpacity = opacity
    }
}
```

### shake
진동 애니메이션
```swift
extension UIView {
    func shake(count : Float = 2,for duration : TimeInterval = 0.15,withTranslation translation : Float = 5) {

        let animation = CAKeyframeAnimation(keyPath: "transform.translation.x")
        animation.timingFunction = CAMediaTimingFunction(name: CAMediaTimingFunctionName.linear)
        animation.repeatCount = count
        animation.duration = duration/TimeInterval(animation.repeatCount)
        animation.autoreverses = true
        animation.values = [translation, -translation]
        layer.add(animation, forKey: "shake")
    }
}
```

### specific corners


```swift
func roundCorners(corners: CACornerMask, radius: CGFloat) {
    layer.maskedCorners = corners
    layer.cornerRadius = radius
}
```


## UIImageView

### load(urlString:)
imageCache를 사용해 한 번 받은 이미지는 더 이상 다시 요청하지 않음

```swift
let imageCache = NSCache<NSString, UIImage>()

extension UIImageView {
    func load(urlString: String) {
        
        guard let url: URL = URL(string: urlString) else {
            return
        }
        
        image = nil
        
        if let imageFromCache = imageCache.object(forKey: urlString as NSString) {
            self.image = imageFromCache
            return
        }
        
        DispatchQueue.global().async { [weak self] in
            if let data = try? Data(contentsOf: url) {
                DispatchQueue.main.async {
                    let imageToCache = UIImage(data: data)
                    
                    imageCache.setObject(imageToCache!, forKey: urlString as NSString)
                    
                    self?.image = imageToCache
                }
            } else {
                print("image is nil, \(urlString)")
            }
        }
    }
}
```

### setImageColor
``` swift
extension UIImageView {
  func setImageColor(color: UIColor) {
    let templateImage = self.image?.withRenderingMode(.alwaysTemplate)
    self.image = templateImage
    self.tintColor = color
  }
}
```

## UIImage

### scaled
```swift
extension UIImage {
    
    /// image를 주어진 size로 렌더링해서 반환합니다.
    /// - Parameters:
    ///   - size: 새로 draw할 size
    ///   - renderingMode: default: alwaysTemplate
    func scaled(to size: CGSize, renderingMode: RenderingMode = .alwaysTemplate) -> UIImage? {
        UIGraphicsBeginImageContext(size)
        self.draw(in: CGRect(x: 0, y: 0, width: size.width, height: size.height))
        let newImage = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
        return newImage?.withRenderingMode(renderingMode)
    }
}
```

## UITextView

### numberOfLine
```swift
extension UITextView {
    func numberOfLine() -> Int {

        let size = CGSize(width: frame.width, height: .infinity)
        let estimatedSize = sizeThatFits(size)
        
        return Int(estimatedSize.height / (self.font!.lineHeight))
    }
}
```

### centerVertically
```swift
extension UITextView {
    func centerVertically() {
        let fittingSize = CGSize(width: bounds.width, height: CGFloat.greatestFiniteMagnitude)
        let size = sizeThatFits(fittingSize)
        let topOffset = (bounds.size.height - size.height * zoomScale) / 2
        let positiveTopOffset = max(1, topOffset)
        contentOffset.y = -positiveTopOffset
    }
}
```

## UINavigationController

### pushVC
make push animation programmatically
``` swift
extension UINavigationController {
    func pushVC(to destination: UIViewController) {
        let transition = CATransition()
        transition.duration = 0.5
        transition.timingFunction = CAMediaTimingFunction(name: .easeInEaseOut)
        transition.type = .push
        transition.subtype = .fromTop
        self.view.layer.add(transition, forKey: kCATransition)
        self.pushViewController(destination, animated: false)
    }
}

```
