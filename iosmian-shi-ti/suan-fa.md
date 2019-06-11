# 算法

---

> ## 算法奇数排在前面，偶数排在后面

```objectivec
+ (NSArray *)kQ905ArraySortArrayByParity:(NSArray *)input {
    // 位移运算，判断是否是偶数 (int & 1) == 0

    if (![input isKindOfClass:[NSArray class]]) {
        return @[];
    }

    if (input.count <= 1) {
        return input;
    }

    NSMutableArray *returnArray = [NSMutableArray arrayWithArray:input];
    NSInteger left              = 0;
    NSInteger right             = (NSInteger)returnArray.count - 1;

    while (left < right && right > left) {
        // odd
        while (([returnArray[left] integerValue] & 1) == 1 && left < right) {
            left += 1;
        }

        // even
        while (([returnArray[right] integerValue] & 1) == 0 && right > left) {
            right -= 1;
        }

        if (left != right) {
            NSNumber *temp     = returnArray[left];
            returnArray[left]  = returnArray[right];
            returnArray[right] = temp;
            left  += 1;
            right -= 1;
        }
    }

    return [returnArray copy];
}
```

> ## Q3 - 无重复字符的最长子串



