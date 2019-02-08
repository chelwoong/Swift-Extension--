# Swift extension Collection 

유용한 swift extension 모아보기! 더 좋은 방법, 코드를 위해 많은 조언과 지적. 감사드립니다.

- [Int](#Int)
    - [제곱](#제곱)
    - [최대공약수](#최대공약수)
    - [최소공배수](#최소공배수)


## Int

### 제곱
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

### 최대공약수 (Greatest common divisor)
``` swift
extension Int {
    func Gcd(_ a: Int, _ b: Int) -> Int {
        return (a % b == 0) ? b : Gcd(b, a%b)
    }
}
```

### 최소공배수 (Least common multiple)
_[최대공약수](#최대공약수) 활용_
``` swift
extension Int {
    func Lcm(_ a: Int, _ b: Int) -> Int {
        return (a * b) / Gcd(a, b)
    }
}
```
