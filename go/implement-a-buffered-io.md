---
description: Implement BufferedFileWriter
---

# Implement a buffered IO

In golang, _bufio.NewWriter_ implements buffered IO.

We implement a BufferedFileWriter, using _os.File.Write_ interface.

```go
// Directly write IO without buffer
func DirectWrite(outFile string) {
	fout, err := os.OpenFile(outFile, os.O_WRONLY|os.O_CREATE|os.O_TRUNC, 0666)
	if err != nil {
		panic(err)
	}
	defer fout.Close()
	for i := 0; i < 10000; i++ {
		fout.WriteString("abcd")
	}
}
```

Then we need design own struct&#x20;

```go
type BufferedFileWriter struct {
	fout *os.File
	cache []byte
	cacheSize int
	cacheEndIndex int
}

// Initialize a buffered writer
func NewBufferedWriter(fout *os.File, cacheSize int) *BufferedFileWriter {
	return &BufferedFileWriter{
		fout: fout,
		cache: make([]byte, cacheSize),
		cacheSize: cacheSize,
		cacheEndIndex: 0,
	}
}

// Core write buffer operation
func (w *BufferedFileWriter) Write(s []byte) {
	if len(s) >= w.cacheSize { // direct write into memory
		w.Flush()
		w.fout.Write(s)
	} else {
		if len(s) + w.cacheEndIndex >= w.cacheSize {
			w.Flush()
		}
		copy(w.cache[w.cacheEndIndex : ], s)
		cacheEndIndex += len(s)
	}	
}

// Write all buffer data into memory
func (w *BufferedFileWriter) Flush() {
	w.fout.Write(w.cache[0 : w.cacheEndIndex])
	w.cacheEndIndex = 0
}

// Encap
func (w *BufferedFileWriter) WriteString(s string) {
	w.Write([]byte(s))
}
```

1. If content is longer than buffer size, directly write into memory bypassing buffer
2. Else if content is longer than rest of buffer size, flush current buffer into memory and then write the new content into buffer.
3. Else write the content into buffer where the offset points to.

Then the DirectWriter could be:

```go
func BufferedWrite(outFile string) {
	fout, err := os.OpenFile(outFile, os.O_WRONLY|os.O_CREATE|os.O_TRUNC, 0666)
	if err != nil {
		panic(err)
	}
	defer fout.Close()

	bwriter := NewBufferedWriter(fout, 4096)
	defer bwriter.Flush() // remember to flush

	for i := 0; i < 10000; i++ {
		bwriter.WriteString("abcd")
	}
}

func main() {
	begin := time.Now()
	DirectWrite("file/data/a.txt")
	BufferedWrite("file/data/a.txt")
	fmt.Println(" %d ms\n", time.Since(begin).MilliSecond())
}
```
