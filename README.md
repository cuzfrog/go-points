# go-points

My version of effective go.

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
### 2. Embed delegate type for "abstract method"

```go
type myInterface interface {
	shared() //same code for multiple implementations 
	forked() //different code
}
```

```go
type delegate struct {
	forkImpl func()
}

func (d *delegate) forked() {
	d.forkImpl() //delegation
}

func (d *delegate) shared() {
	//some reused code
}
```

```go
type myImpl struct {
	*delegate
}

func New() myInterface{
	d := new(delegate)

	d.forkImpl = func() {
		//specific impl
	}

	return &myImpl{d}
}
```
Let's later see if this conflicts with "With Go-like type composition the embedded types should generally be 'whole', complete and useful on their own."
