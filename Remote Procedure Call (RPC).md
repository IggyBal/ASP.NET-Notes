[^techtarget]
- software communication protocol
- requests service from remote program
	- calls other processes on remote system
- *procedure call* -> functional call/subroutine call

- [[Client-server model]]
	- client: request program
	- server: service-providing program
- synchronous operation **requiring request program to be suspended** until results return
	- using processes or threads sharing the same address space enables multiple RPCs to **perform concurrently**

- program statements using RPC framework are compiled into executable
	- *stub* included in compile code -> representative of remote procedure code
### RPC vs REST vs AJAX
 [^reddit]
- RPC and REST can use AJAX
- REST uses HTTP methods (e.g. `CREATE`, `POST`)
- RPC expects all requests to be `POST` requests (e.g. `POST`to /user/create can call a 'create' method of the 'user' service)
### References
 [^techtarget]: https://www.techtarget.com/searchapparchitecture/definition/Remote-Procedure-Call-RPC
 [^reddit]: https://www.reddit.com/r/webdev/comments/3eb31p/can_someone_please_clarify_usages_of_jsonrpc_ajax/
 