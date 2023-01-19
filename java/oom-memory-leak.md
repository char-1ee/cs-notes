# OOM, memory leak

OOM stands for Out Of Memory error. This is an error thrown by Java VMs (virtual machine) when they run out of heap space, where all the Java Objects are stored.

When the VM is running low on heap space it generally triggers a GC (garbage collection). GC is an automated routine that destroys unused objects from the heap, thereby reclaiming free space for future use. The garbage collector knows which objects to destroy by checking their reference counts. If an object is not is referenced, then it can be garbage collected.

A memory leak is a case when an object that is not needed anymore is still being referenced. Thus, even though the garbage collector runs, the useless object cannot be destroyed. If the program holds many such useless objects in reference (mostly due to bad coding), the heap space keeps growing and can result in an OOM.

* OOM: no more can use, memory leak: not collected&#x20;
* Growing memory leak results in OOM
