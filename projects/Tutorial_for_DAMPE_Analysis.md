# Tutorial for DAMPE Analysis

[toc]

## Preface

<div style="background-color: #f8d7da; color: #721c24; border: 5px solid #f5c6cb; padding: 15px; border-radius: 5px;">
<strong>use of Visual Studio Code</strong>
    We strongly discourage the use of VSCode as a remote editor. The VSCode server application, that is always and surreptitiously installed on the remote servers, consumes a significant amount of RAM, swap, and home disk memory (<code>du -hsx ~/.vscode-server</code>) on the remote machine, leading to slowness, performance issues, and even malfunctions impeding the work of other users.
</div>

Step 0. Ask supervisor to add you to the mailing list of DAMPE and ask for the permission to the DAMPE Twiki

[mailing list](mailto:dampe-cr@pmo.ac.cn,dampe-pb@pmo.ac.cn,dampe-photon@pmo.ac.cn,dampe-simu@pmo.ac.cn)

[DAMPE Twiki ](https://twiki.cern.ch/twiki/bin/viewauth/DAMPE/WebHome)

Step 1. https://www.ac.infn.it/associazioni/PaginaPubblicaAssociazioni/index.html

**Sign Up**  &rarr; IoA1 &rarr; **User Portal** &rarr; Identical Verification via web meeting and upload your password &rarr; loA2 &rarr; E-Learning Course (Network Security) &rarr; Passed &rarr; **Gestione Associazioni** &rarr; Affiliation Process and Sign Contract

Step 2. EDH-account for CERN

Supervisor approve &rarr; pre-registration &rarr; E-Learning Course (Network Security) &rarr; Passed

Step 3. CNAF-INFN account https://www.cnaf.infn.it/en/users-faqs/

Fill the form &rarr; Get your reference person in CNAF &rarr; submission

## Build your workflow

Windows: MobaXterm (Terminal), VSCode

MacOS (Apple Silicon): Terminal, VSCode

### Build VsCode workflow

Step1. Go to the path `/username/.ssh`

```shell
cd /username/.ssh
```

Step2. Create the public key and private key via the command in the Terminal (Windows or Mac)

```shell
# /username/.ssh
ssh-keygen
```

This command will produce the public key `XXX.pub` and the  private key `xxx`. The private key you should key by yourself, and the public key you could distribute it to others, such as the remote computation farm we login. ! Generating public/private ed25519 key pair !

Step3. Create a file name `config` under the path `/username/.ssh` and save it.

```shell
# /username/.ssh/config

Host bastion 
    HostName bastion.cnaf.infn.it
    User xiongzheng
    IdentityFile ~/.ssh/xxx # private key
    ForwardX11 yes
    ForwardX11Trusted yes

Host cnaf-dampe
    HostName ui-tier1.cr.cnaf.infn.it
    ProxyCommand ssh bastion -W %h:%p
    User xiongzheng
    IdentityFile ~/.ssh/xxx # private key
    ForwardX11 yes
    ForwardX11Trusted yes

Host dampe-workflow
    HostName dampevm1.unige.ch
    ProxyCommand ssh cnaf-dampe -W %h:%p
    User workflow
    IdentityFile ~/.ssh/xxx # private key
    ForwardX11 yes
    ForwardX11Trusted yes
```

Step5. login the farm and save the content of public key `xxx.pub`  under the file `~/.ssh/authorized_key`. Then change the mode of the path `~/.ssh/` and file `~/.ssh/authorized_key`

```shell
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_key
```

The order of dealing with the farms:

```
bastion.cnaf.infn.it
ui-tier1.cr.cnaf.infn.it
dampevm1.unige.ch
```

![image-20250122230916310](..\blogs\DAMPE\image-20250122230916310.png)

### Build Termial Workflow

`local`,`bastion`, and `cnaf-dampe` hosts generate public and private keys using ssh-keygen.

The public key authentication chain is as follows:

- Add `local`’s public key to the ~/.ssh/authorized_keys file on host `bastion`.
- Add `bastion`’s public key to the ~/.ssh/authorized_keys file on host `cnaf-dampe`.
- Add `cnaf-dampe`’s public key to the ~/.ssh/authorized_keys file on host `dampe-workflow`.

Done.

Reference

[1] https://confluence.infn.it/spaces/TD/pages/40665299/INFN-CNAF+Tier-1+User+Guide+July+2024+-+v19

[2] http://afsapply.ihep.ac.cn/cchelp/zh/

[3] https://www.cnaf.infn.it/~usersupport/XrootD_SA.html

[4] https://code.visualstudio.com/blogs/2019/10/03/remote-ssh-tips-and-tricks
