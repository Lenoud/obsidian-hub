# OpenEuler 22.03换源

openEuler 22.03 系统更换软件源配置。



## 换完源后执行：
```bash
sudo dnf clean all
sudo dnf makecache
```

## 清华源
```bash
#generic-repos is licensed under the Mulan PSL v2.
#You can use this software according to the terms and conditions of the Mulan PSL v2.
#You may obtain a copy of Mulan PSL v2 at:
#    http://license.coscl.org.cn/MulanPSL2
#THIS SOFTWARE IS PROVIDED ON AN "AS IS" BASIS, WITHOUT WARRANTIES OF ANY KIND, EITHER EXPRESS OR
#IMPLIED, INCLUDING BUT NOT LIMITED TO NON-INFRINGEMENT, MERCHANTABILITY OR FIT FOR A PARTICULAR
#PURPOSE.
#See the Mulan PSL v2 for more details.

[OS]
name=OS
baseurl=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/OS/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/OS/$basearch/RPM-GPG-KEY-openEuler

[everything]
name=everything
baseurl=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/everything/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/everything/$basearch/RPM-GPG-KEY-openEuler

[EPOL]
name=EPOL
baseurl=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/EPOL/main/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/OS/$basearch/RPM-GPG-KEY-openEuler

[debuginfo]
name=debuginfo
baseurl=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/debuginfo/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/debuginfo/$basearch/RPM-GPG-KEY-openEuler

[source]
name=source
baseurl=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/source/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/source/RPM-GPG-KEY-openEuler

[update]
name=update
baseurl=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/update/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.tuna.tsinghua.edu.cn/openeuler/openEuler-22.03-LTS/OS/$basearch/RPM-GPG-KEY-openEuler

```

## 阿里源

```bash
[OS]
name=OS
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/OS/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/OS/$basearch/RPM-GPG-KEY-openEuler

[everything]
name=everything
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/everything/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/everything/$basearch/RPM-GPG-KEY-openEuler

[EPOL]
name=EPOL
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/EPOL/main/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/OS/$basearch/RPM-GPG-KEY-openEuler

[debuginfo]
name=debuginfo
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/debuginfo/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/debuginfo/$basearch/RPM-GPG-KEY-openEuler

[source]
name=source
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/source/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/source/RPM-GPG-KEY-openEuler

[update]
name=update
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/update/$basearch/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/OS/$basearch/RPM-GPG-KEY-openEuler

[update-source]
name=update-source
baseurl=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/update/source/
enabled=1
gpgcheck=1
gpgkey=https://mirrors.aliyun.com/openeuler/openEuler-22.03-LTS-SP2/source/RPM-GPG-KEY-openEuler
```
