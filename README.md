## 1. Giới thiệu về HTTP Live Streaming (HLS)

HTTP Live Streaming (hay còn được biết đến là HLS) là một cách truyền media dựa trên giao thức HTTP được Apple phát triển. Nó hỗ trợ các luồng trực tuyến, có khả năng thay đổi chất lượng phù hợp với thiết bị và băng thông mạng đang sử dụng. Cụ thể, giao thức làm việc như sau

- Một tệp tin hay một luồng live sẽ được chia thành các file nhỏ
- Các file nhỏ bên trên sẽ được lưu trữ trong một máy chủ web và lắng nghe các request từ một trình player.
- Khi phát, player sẽ phát liên tiếp các file nhỏ một cách liền mạch mà không bị ngắt quãng

Nếu stream được chia thành nhiều chất lượng khác nhau (480p, 720p), thì player sẽ tự động lựa chọn chất lượng video tốt nhất để phát dựa theo tình trạng băng thông mạng. Thuật ngữ này là Adaptive Streaming (Thích nghi với điều kiện).

<img src="https://support.jwplayer.com/customer/portal/attachments/238062" />

=======
## HƯỚNG DẪN TẠO STREAM SERVER VÀ CHUYỂN ĐỔI VIDEO THƯỜNG SANG STREAMING
>>>>>>> origin/master

Powered by <a href="http://meditech.vn">MediTech,. JSC</a> & <a href="http://longvanidc.vn">LongVanIDC</a>

## 2. Hướng dẫn tạo server video streaming

##### Thông tin về server cài đặt

```

OS: Ubuntu 16.04.4 LTS
Internet: Có (Bắt buộc)

```

### Cài đặt ffmpeg để chuyển đổi video thường sang dạng Streaming (ts)

Follow this instruction: [http://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu](Compile FFmpeg on Ubuntu, Debian, or Mint 

#### Sau khi biên dịch đủ 9 gói, chúng ta gõ lệnh `ffmpeg` để kiểm tra

<img src="http://image.prntscr.com/image/78cfb8e77260481e9e5b1095c91167e3.png" />

### Chuyển đổi video thường sang dạng Streaming (ts)

```
ffmpeg -y -i input.mp4 -r 25 -g 25 -c:a libfdk_aac -b:a 128k -c:v libx264 -preset veryfast -b:v 1600k -maxrate 1600k -bufsize 800k -s 640x360 -c:a libfdk_aac -vbsf h264_mp4toannexb -flags -global_header -f ssegment -segment_list playlist.m3u8 -segment_list_flags +live-cache -segment_time 5 output-%04d.ts
```

- `input.mp4`: Video có định dạng thông thường có thể như AVI, MPG, MKV,...
- `playlist.m3u8`: Playlist chứa thông tin các file stream
- `output-%04d`: File stream có dạng output-0001.ts, output-000n.ts

### Cài đặt Web Server để players chạy stream

Chúng ta cài đặt NGINX

```
yum install -y nginx
service nginx start
chkconfig nginx on
```

Tắt SELinux và mở port 80 trên iptables

```
sed s/"SELINUX=enforcing"/"SELINUX=disabled"/g /etc/sysconfig/selinux
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
service iptables save
service iptables restart
```

Copy các file stream, playlist vào một thư thục và chuyển chúng tới thư mục public html của bạn.

Mặc định, thư mục public của nginx ở CentOS

```
/usr/share/nginx/html
```


<img src="http://image.prntscr.com/image/57939a7e0e6d4510a74d75ea03bb3fac.png" />

=======
<img src="http://image.prntscr.com/image/57939a7e0e6d4510a74d75ea03bb3fac.png"/>

Địa chỉ stream của tôi: http://192.168.100.192/bai-hat-abc/playlist.m3u8

