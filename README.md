<h1 align="center">Package for Merkle Tree in Go</h1>

#### Install
```
go get github.com/avvvet/merkletree
```

### Usage
```go
package main
import (
  "crypto/sha256"
  "log"
  "github.com/avvvet/merkletree"
)

type LeafContent struct {
  content string
}

//CalculateHash hashes the values of a LeafContent
func (t LeafContent) GenerateHash() ([]byte, error) {
  h := sha256.New()
  if _, err := h.Write([]byte(t.content)); err != nil {
    return nil, err
  }
  return h.Sum(nil), nil
}

//Equals tests for equality of two Contents
func (t LeafContent) EqualsToHash(other merkletree.Content) (bool, error) {
  return t.content == other.(LeafContent).content, nil
}

func main() {
  //Build list of Content to build tree
  var list []merkletree.Content
  list = append(list, LeafContent{content: "Yellow"})
  list = append(list, LeafContent{content: "Green"})
  list = append(list, LeafContent{content: "Blue"})
  
  //Create a new Merkle Tree from the list of Content
  t, err := merkletree.NewTree(list)
  if err != nil {
    log.Fatal(err)
  }

  //Get the Merkle Root of the tree
  mr := t.MerkleRoot()
  log.Prinf("Merkle root %x", mr)
  
  //Verify the entire tree (hashes for each node) is valid
  vt, err := t.VerifyTree()
  if err != nil {
    log.Fatal(err)
  }
  log.Println("Verify Tree: ", vt)
 
 //Verify a specific content in in the tree
  vc, err := t.VerifyContent(list[0])
  if err != nil {
    log.Fatal(err)
  }
  log.Println("Verify Content: ", vc)
  
  //String representation
  log.Println(t)
}
```