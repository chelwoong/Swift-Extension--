# Swift extension Collection 

유용한 swift extension 모아보기! 더 좋은 방법, 코드를 위해 많은 조언과 지적. 감사드립니다.
- [String](#string)
    - [진법변환](#진법변환)

- [Int](#int)
    - [거듭제곱](#거듭제곱)
    - [최대공약수와 최소공배수](#최대공약수와-최소공배수)

- [UIColor](#uicolor)
    - [hexString](#hexstring)
    
- [UIView](#uiview)
    - [anchor](#anchor)
    
- [UINavigationController)(#uinavigationcontroller)
    - [pushVC](#pushvc)

## String

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

## UINaviagtionController

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
