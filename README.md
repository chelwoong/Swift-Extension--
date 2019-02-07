# Swift extension Collection 

유용한 swift extension 모아보기! 더 좋은 방법, 코드를 위해 많은 조언과 지적. 감사드립니다.


## Int

### 제곱
```swift
extension Int {
    func square(_ num: Int) -> Int {
        var result = self
        print(result)
        for _ in 0..<num {
            result *= self
            print(result)
        }
        return result
    }
}
```
