Linux Kernel for Open-Channel SSD, CNEX SDK
==========================================

CNEX Labs에서 CNEX SDK를 위한 작업이 처리된 branch를 메인으로 하고 있습니다. 
2018년 6월 26일을 기준으로 연구실에서 보유한 CNX-8800이 이 커널 위에서 정상 작동합니다. 

How to install this kernel to PC?
--------------------------------

git clone 또는 zip으로 압축된 폴더를 다운로드한 뒤, 커널 configuration을 실행합니다. 

	$ sudo make menuconfig

설정 시에는 특별히 건드릴 내용은 없으며, 단순히 설정을 끝내고 해당 설정을 저장하도록 exit, yes 선택합니다. 
이후 해당 폴더에 있는 .config 숨김 파일을 확인하여, 다음 네 가지 항목이 Y로 체크되어 있는지 확인합니다. 

	1. CONFIG_BLK_DEV_NVME=y
	2. CONFIG_NVM=y
	3. CONFIG_NVM_DEBUG=y
	4. CONFIG_NVM_PBLK=y

이후 커널을 컴파일합니다. 

	$ sudo make -j {number of cores}
	$ sudo make modules_install
	$ sudo make install

How to mount Open-Channel SSD to linux?
--------------------------------------

다음과 같이 디바이스를 검색합니다. 

	$ sudo nvme lnvm list

발견된 디바이스 이름이 nvme0n1인 경우, 아래의 명령어를 통해 pblk을 적용하는 block device로 등록할 수 있습니다. 

	$ sudo nvme lnvm create -d nvme0n1 -n {device name} -t pblk -b 0 -e 127 -f

-b와 -e는 각각 해당 block device에 할당할 physical unit의 시작 번호와 끝 번호를 나타내며, 
-n 뒤에 주는 device name이 실제로 I/O 시에 참조될 이름이 됩니다. 
-f 옵션은 기존에 쓰여진 데이터를 지우는 것으로, -f를 추가하지 않으면 전체 디바이스를 탐색하며 기존의 매핑 데이터를 불러옵니다. 

	$ sudo mkfs.ext4 /dev/{device name}
	$ mount /dev/{device name} {directory to mount}

특별한 문제가 없었다면 위 명령어를 통해 EXT4 파일 시스템을 해당 block device에 만들고 mount할 수 있습니다. 
 
추가 정보가 필요한 경우, 아래 링크 등을 확인하시기 바랍니다. 

* http://openchannelssd.readthedocs.io/en/latest/ 
* https://www.usenix.org/conference/fast17/technical-sessions/presentation/bjorling 

Linux kernel
============

This file was moved to Documentation/admin-guide/README.rst

Please notice that there are several guides for kernel developers and users.
These guides can be rendered in a number of formats, like HTML and PDF.

In order to build the documentation, use ``make htmldocs`` or
``make pdfdocs``.

There are various text files in the Documentation/ subdirectory,
several of them using the Restructured Text markup notation.
See Documentation/00-INDEX for a list of what is contained in each file.

Please read the Documentation/process/changes.rst file, as it contains the
requirements for building and running the kernel, and information about
the problems which may result by upgrading your kernel.
