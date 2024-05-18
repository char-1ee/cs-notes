# Copy-on-write

### How Copy-on-Write Works

When a system uses copy-on-write, it initially allows multiple users or processes to share a single copy of a resource (like a memory block or a file) while it appears as if each user has their own separate copy. Each user thinks they have their own independent copy because the system presents it that way. However, the actual physical copy of the resource is not duplicated.

When one of the users makes a modification to this shared resource, the system:

1. Creates a new copy of the resource at the moment of modification.
2. Applies the changes to the new copy.
3. Redirects the modifying user to the new copy.
4. Leaves the original resource unchanged for users who have not made any modifications.

This process ensures that only necessary copies are made, thereby saving memory and reducing the overhead of managing multiple copies of data that are identical.

### How Changes are Handled After Copy-on-Write

1. **Continued Isolation**:
   * **In many cases**, after the copy-on-write operation, the modifying user or process continues to work with the new copy independently. Other users or processes continue to access the original version without seeing the new changes. This approach is common in systems where isolation of changes is desired, such as in process forking in operating systems or in snapshot functionalities in file systems.
   * **Example**: In a virtualized environment, if a virtual machine modifies a disk sector, the changes are written to a new sector specifically allocated to that VM. Other VMs using the same base image remain unaffected by these changes.
2. **Synchronization Requirements**:
   * If changes made to the copied resource need to be visible to other users or processes, additional synchronization mechanisms must be implemented outside of the basic copy-on-write strategy. This could involve updating references, sending notifications, or merging changes, depending on the application.
   * **Example**: In a collaborative application, if a document is edited and needs to be updated across all users’ views, the application might use a version control system or a real-time broadcasting mechanism to ensure that all participants see the latest changes.

After the first modification triggers a copy-on-write, resulting in a new version of the resource (such as a memory page or a file), subsequent access and synchronization depend on the goals of the system:

* **Version Control**: Systems that need to manage multiple versions of data, such as databases or collaborative editing tools, often use version control mechanisms. These systems can track and merge changes, and provide users with the most recent version of the data.
* **Event-Based Notifications**: Some systems use event-driven architectures to notify interested parties of changes. For example, when a new copy is written and modified, an event can be triggered to inform other components of the system about the update.
* **Shared State Management**: In distributed systems, managing the state across multiple nodes often involves consensus protocols or distributed ledgers to ensure that all nodes agree on the current state of the data.
* **Explicit Merge Operations**: In some contexts, it might be necessary for users or processes to manually or programmatically merge changes from different versions of a resource. This is common in software development environments using tools like Git.

### Code example

```go
type tmap map[int]string

type CowMap struct {
	mu   sync.Mutex
	data atomic.Value
}

func NewCowMap() *CowMap {
	m := make(tmap)
	c := &CowMap{}
	c.data.Store(m)
	return c
}

func (c *CowMap) clone() tmap {
	m := make(tmap)
	for k, v := range c.data.Load().(tmap) {
		m[k] = v
	}
	return m
}

func (c *CowMap) Get(key int) (value string, ok bool) {
	value, ok = c.data.Load().(tmap)[key]
	return
}

func (c *CowMap) Set(key int, value string) {
	c.mu.Lock()
	defer c.mu.Unlock()

	//写之前，先拷贝一个副本
	copy := c.clone()
	//修改副本
	copy[key] = value
	//修改副本数据后，将副本转正
	c.data.Store(copy)
}

func readLoop(c *CowMap) {
	for {
		fmt.Println(c.Get(3))
	}
}

func writeLoop(c *CowMap) {
	for i := 0; i < 10000000; i++ {
		c.Set(3, "werben-"+strconv.Itoa(i))
	}
}

func main() {
	c := NewCowMap()
	c.Set(1, "a")
	c.Set(2, "b")
	c.Set(3, "c")

	go readLoop(c)
	writeLoop(c)
}
```

In this scenario, there is no need to maintain call RLock for readLoop when no write operations happen.

### Applications

1. **Operating Systems**: Many modern operating systems use copy-on-write for managing memory pages. When a process is forked, the operating system allows the parent and child processes to share the same pages in memory. If either process modifies these pages, a new copy of the page is made (copy-on-write).
2. **Programming**: In programming, certain data structures and objects use copy-on-write to manage memory more efficiently. For instance, some implementations of strings or arrays only duplicate data when a modification occurs.
3. **Virtualization**: Virtual machines often use copy-on-write to manage disk images. When a virtual machine is cloned, the clone can initially share the same disk image as its source. If changes are made to the disk by the clone, those sectors are copied and modified, leaving the original disk image intact.
4. **File Systems**: Some advanced file systems, like ZFS and Btrfs, use copy-on-write to handle data storage and snapshots efficiently. When a file is modified, only the changed blocks are written to new locations, with metadata updated to reflect the change. This approach can enhance data integrity and recovery.
