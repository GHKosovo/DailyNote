编号325967：如何确定值的正确显示

```java
return new BigDecimal(data.toString()).stripTrailingZeros().toPlainString();
```

