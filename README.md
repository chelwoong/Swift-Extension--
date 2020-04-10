# Swift extension Collection 

유용한 swift extension 모아보기! 더 좋은 방법, 코드를 위해 많은 조언과 지적. 감사드립니다.
- [String](#string)
    - [문자열 자르기](#문자열-자르기)
    - [진법변환](#진법변환)
    - [addLineSpacing](#addlinespacing)

- [Int](#int)
    - [거듭제곱](#거듭제곱)
    - [최대공약수와 최소공배수](#최대공약수와-최소공배수)
    - [소수](#소수)
    
- [Date](#date)

- [UIColor](#uicolor)
    - [hexString](#hexstring)
    
- [UIView](#uiview)
    - [addLine](#addline)
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

- [UITextView](#uitextview)
    - [numberOfLine](#numberofline)

- [UINavigationController](#uinavigationcontroller)
    - [pushVC](#pushvc)

## String

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

### 최대공약수와 최소공배수

``` swift
extension Int {

    // Greatest common divisor
    func Gcd(_ a: Int, _ b: Int) -> Int {
        return (a % b == 0) ? b : Gcd(b, a%b)
    }
    
    // Least common multiple
    func Lcm(_ a: Int, _ b: Int) -> Int {
        return (a * b) / Gcd(a, b)
    }
}
```

### 소수
```swift
extension Int {
    if self < 2 {
        return false
    }
    
    func isPrime() -> Bool {
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
        var int = UInt32()
        Scanner(string: hex).scanHexInt32(&int)
        let a, r, g, b: UInt32
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

viewWillLayoutSubviews에서 사용해야 함

```swift
func roundCorners(corners: UIRectCorner, radius: CGFloat) {
    let contentViewLayer = CAShapeLayer()
    let contentViewPath = UIBezierPath(
        roundedRect: self.bounds,
        byRoundingCorners: corners,
        cornerRadii: CGSize(width: radius, height: radius)
    ).cgPath
    contentViewLayer.path = contentViewPath
    contentViewLayer.fillColor = UIColor.white.cgColor
    self.layer.insertSublayer(contentViewLayer, at: 0)
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
