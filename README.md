# Swift extension Collection 

유용한 swift extension 모아보기! 더 좋은 방법, 코드를 위해 많은 조언과 지적. 감사드립니다.
- [String](#String)
    - [진법변환](#진법변환)
- [Int](#Int)
    - [거듭제곱](#거듭제곱)
    - [최대공약수](#최대공약수)
    - [최소공배수](#최소공배수)


## String

### 진법변환
_[거듭제곱](#거듭제곱) 활용_
현재 진법에서 주어진 진법으로 변환해주는 extension, 단, 2 ~36진법까지 가능하며 10부터는 A~Z로 매칭한다.

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

### 최대공약수
``` swift
extension Int {
    // Greatest common divisor
    func Gcd(_ a: Int, _ b: Int) -> Int {
        return (a % b == 0) ? b : Gcd(b, a%b)
    }
}
```

### 최소공배수 
_[최대공약수](#최대공약수) 활용_
``` swift
extension Int {
    // Least common multiple
    func Lcm(_ a: Int, _ b: Int) -> Int {
        return (a * b) / Gcd(a, b)
    }
}
```
