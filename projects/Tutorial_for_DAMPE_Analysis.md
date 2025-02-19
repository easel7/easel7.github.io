# Tutorial for DAMPE Analysis

[toc]

## 0. Preface

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



### Usefull Shortcuts

1. [PMO Indico Page](https://indico.pmo.ac.cn/category/4/)
2. [PMO indico page](https://indico.pmo.ac.cn/event/826/)
3. [DMPSW code info](http://119.78.211.2:10088/SVNDAMPE/rep1/) (user:tutorial, pwd:tutorial)
4. [DAMPE Twiki](https://twiki.cern.ch/twiki/bin/view/DAMPE/WebHome) - home (reference for any doubt)
5. [SimulationGroup info](https://twiki.cern.ch/twiki/bin/view/DAMPE/DampeSimulation) DAMPE CR nuclei MC production 
   - [MC Statistics](https://docs.google.com/spreadsheets/d/1-cvwk-k3zHKg0h5rklu5lhlrfOlsr5ep6WkQeeGW_6M/edit?pli=1&gid=0#gid=0)
   - [Shift] (https://docs.google.com/spreadsheets/d/1khi1yaOGAAdarkfonenVrifoB40HFYax7ghw7gN2qos/edit?gid=1049189058#gid=1049189058)
6. [DAMPE publications](https://dpnc.unige.ch/dampe/publication.html)


## 1. Build your workflow

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

2. ## 

```
B4.root
├─particleName (1-proton, 2-deurteron)
├─energy (Mev)
├─Layer0 E dep(MeV)
├─Layer1 E dep(MeV)
├─...
├─Layer13 E dep(MeV)
├─Total E dep(MeV)
├─Layer0 Track Length(mm)
├─Layer1 Track Length(mm)
├─...
├─Layer13 Track Length(mm)
├─Total Track Length(mm)
├─First_Depth (mm)
├─First_Layer 
├─First_Type (0-electromagntic, 1-hadronic, 3-others)
└─First_Second (number of Secondaries)
```



```
        |<----layer 0---------->|<----layer 1---------->|<----layer 2---------->|
        |                       |                       |                       |
        ==========================================================================
        ||              |       ||              |       ||              |       ||
        ||              |       ||              |       ||              |       ||
 beam   ||   absorber   |  gap  ||   absorber   |  gap  ||   absorber   |  gap  ||
======||              |       ||              |       ||              |       ||
        ||              |       ||              |       ||              |       ||
        ==========================================================================
```

# Geant4 manual installation on macOS

http://geant4-dna.in2p3.fr/styled-6/styled-12/index.html 

Cmake command to build

```shell
cmake -DCMAKE_INSTALL_PREFIX=/Users/xiongzheng/software/build/geant4-v11.3.0-install -DCMAKE_BUILD_TYPE=RelWithDebInfo -DGEANT4_USE_GDML=ON -DXERCESC_ROOT_DIR=/opt/homebrew/opt/xerces-c -DGEANT4_USE_QT=ON -DGEANT4_INSTALL_EXAMPLES=ON -DGEANT4_INSTALL_DATA=ON -DGEANT4_USE_SYSTEM_EXPAT=OFF -DGEANT4_BUILD_TLS_MODEL=auto ../geant4
```

```shell
rm -rf build CMakeCache.txt CMakeFiles
mkdir build
cd build
comp ..
make -j10
```

## X. Reference

[1] https://confluence.infn.it/spaces/TD/pages/40665299/INFN-CNAF+Tier-1+User+Guide+July+2024+-+v19

[2] http://afsapply.ihep.ac.cn/cchelp/zh/

[3] https://www.cnaf.infn.it/~usersupport/XrootD_SA.html

[4] https://code.visualstudio.com/blogs/2019/10/03/remote-ssh-tips-and-tricks
