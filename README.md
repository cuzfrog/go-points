# go-points
my version of effective go

### 1. b.N and benchmark time

This may be going to take a long time:
```go
func BenchmarkCheap(b *testing.B) {
  for i := 0; i < b.N; i++ {
    b.StopTimer()
    s := Expensive()
    b.StartTimer()

    s.Cheap()
  }
}
```
a better way is to prepare data ahead of time:

```go
func BenchmarkCheap(b *testing.B) {
  ss := make([]S, b.N, b.N)
  for i := 0; i < b.N; i++ {
    ss[i] = Expensive()
  }
  b.ResetTimer()
  for i := 0; i < b.N; i++ {
    ss[i].Cheap()
  }
}
```
