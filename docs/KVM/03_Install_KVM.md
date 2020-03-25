# Cài đặt KVM bằng libvirt
## **1) Cài đặt trên CentOS 7**
- **B1 :** Kiểm tra xem **CPU** có hỗ trợ ảo hóa không bằng lệnh sau . Nếu kết quả trả về khác `0` có nghĩa là **CPU** có hỗ trợ :
    ```
    # egrep -c "svm|vmx" /proc/cpuinfo
    4
    ```
- **B2 :** Cài đặt **KVM** và các gói liên quan :
    ```
    # yum install -y qemu-kvm libvirt bridge-utils virt-manager
    ```
    - Trong đó :
        - `qemu-kvm` : phần phụ trợ cho **KVM**
        - `libvirt` : cung cấp libvirt sử dụng để quản lý QEMU và KVM
        - `bridge-utils` : tiện ích sử dụng để tạo vào quản lý các bridge
        - `virt-manager` : cung cấp giao diện để quản lý máy ảo
- **B3 :** Kiểm tra lại xem **KVM** đã được cài vào chưa :
    ```
    # lsmod | grep kvm
    ```
    <img src=https://i.imgur.com/bnXe8aw.png>

- **B4 :** Khởi động dịch vụ `libvirt` và cho phép khởi động cùng hệ thống :
    ```
    # systemctl start libvirtd
    # systemctl enable libvirtd
    ```
- **B5 :** Cài package `virt-install` để cài đặt máy ảo và `virt-viewer` để hiển thị quá trình tạo **VM** :
    ```
    # yum install -y virt-install virt-viewer
    ```
    - Đối với **CentOS 7** bản **minimal** cần cài đặt thêm gói `x-window` để sử dụng được `virt-viewer` và `virt-manager` :
        ```
        # yum install "@X Window System" xorg-x11-xauth xorg-x11-fonts-* xorg-x11-utils -y
        ```
- **B6 :** Tạo 1 thư mục chứa file `.iso` để cài OS trong đường dẫn `/var/lib/libvirt/`
    ```
    # cd /var/lib/libvirt/
    # mkdir file-iso
    ```
    - Tải về file iso của **CentOS 7 Minimal** vào thư mục này :
        ```
        # cd file-iso
        # yum install wget -y
        # wget http://mirrors.nhanhoa.com/centos/7.7.1908/isos/x86_64/CentOS-7-x86_64-Minimal-1908.iso
        ```
- **B7 :** Cài máy ảo với các thông số sau :
    ```
    # virt-install \
    --name=Centos7-test \
    --vcpus=1 \
    --memory=512 \
    --cdrom=/var/lib/libvirt/file-iso/CentOS-7-x86_64-Minimal-1908.iso \
    --disk=/var/lib/libvirt/images/centos7-test,size=3 \
    --os-variant=rhel7 \
    --network bridge=virbr0
    ```
    <img src=https://i.imgur.com/VPgU3Ih.png>

    - Trong đó :
        - `--name` :  tên của máy ảo
        - `--vcpus` : số vCPU gán cho máy ảo
        - `--memory` : lượng RAM cấp cho máy ảo ( đơn bị `MiB` )
        - `--cdrom` : chỉ ra đường dẫn đến file `.iso` dùng để cài đặt OS . Nếu boot bằng cách khác dùng option `--location` sau đó chỉ ra đường dẫn đến file (có thể là đường dẫn đến file trên internet)
        - `--disk, size` : chỉ ra vị trí lưu disk máy ảo với disk có size là bao nhiêu ( đơn vị `GiB` )
        - `--os-variant` : loại OS đang tạo. Option này có thể chỉ ra hoặc không nhưng nên sử dụng nó vì nó sẽ cải thiện hiệu năng của máy ảo sau này. Nếu không biết HĐH bạn đang tạo thuộc loại nào, có thể tìm kiếm thông tin này bằng cách dùng lệnh `osinfo-query os`
        - `--network` : loại mạng mà VM sử dụng
> Quá trình này **KHÔNG ĐƯỢC** Cancel!
- **B8 :** Mở một tab SSH khác tới server . Chạy lệnh `virt-manager` để vào trình quản lý cài đặt máy ảo KVM :
    ```
    # virt-manager
    ```
- **B9 :** Trình quản lý virt-manager hiện lên, chọn vào máy ảo vừa tạo :

    <img src=https://i.imgur.com/2QP5Sgm.png>

- **B10 :** Cài đặt máy ảo như bình thường :

    <img src=https://i.imgur.com/1tN0aBf.png>

## **2) Cài đặt trên Ubuntu**
- **B1 :** Chuyển đổi sang user `root` :
    ```
    cuongnq@ubuntu1804~$: sudo -i
    root@ubuntu1804:~#
    ```
- **B2 :** Kiểm tra xem **CPU** có hỗ trợ ảo hóa không bằng lệnh sau . Nếu kết quả trả về khác `0` có nghĩa là **CPU** có hỗ trợ :
    ```
    # egrep -c "svm|vmx" /proc/cpuinfo
    4
    ```
- **B3 :** Cài đặt **KVM** và các gói liên quan :
    ```
    # apt-get update
    # apt-get install -y qemu-kvm libvirt-bin bridge-utils virt-manager
    ```
- **B4 :** Kiểm tra lại xem **KVM** đã được cài vào chưa :
    ```
    # lsmod | grep kvm
    ```
    <img src=https://i.imgur.com/rhx0nF1.png>

- **B5 :** Tạo 1 thư mục chứa file `.iso` để cài OS trong đường dẫn `/var/lib/libvirt/`
    ```
    # cd /var/lib/libvirt/
    # sudo mkdir file-iso
    ```
    - Tải về file iso của **CentOS 7 Minimal** vào thư mục này :
        ```
        # cd file-iso
        # sudo wget http://mirrors.nhanhoa.com/centos/7.7.1908/isos/x86_64/CentOS-7-x86_64-Minimal-1908.iso
        ```
- **B6 :** Cài máy ảo với các thông số sau :
    ```
    # virt-install \
    --name=Centos7-test \
    --vcpus=1 \
    --memory=512 \
    --cdrom=/var/lib/libvirt/file-iso/CentOS-7-x86_64-Minimal-1908.iso \
    --disk=/var/lib/libvirt/images/centos7-test,size=3 \
    --os-variant=rhel7 \
    --network bridge=virbr0
    ```
- **B7 :** Mở một tab SSH khác tới server . Chạy lệnh `virt-manager` để vào trình quản lý cài đặt máy ảo KVM :
    ```
    # virt-manager
    ```
- **B8 :** Trình quản lý virt-manager hiện lên, chọn vào máy ảo vừa tạo :

    <img src=https://i.imgur.com/C0ce7PG.png>

- **B9 :** Cài đặt máy ảo như bình thường :

    <img src=https://i.imgur.com/fQqrk1i.png>

    