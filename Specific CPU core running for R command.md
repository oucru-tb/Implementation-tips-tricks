<div align='center'>
   <h1> Specify CPU cores while running R script on server </h1>
</div>

## Vấn đề
- Khi sử dụng R, có nhiều function khi thực thi trên server có nguy cơ sẽ gây ra tình trạng crash vì function đó tự mặc định sử dụng hết toàn bộ resources có trong hệ thống để chạy, dù đã set up số lượng cores bằng R
- Ví dụ như hàm JMbayes2::jm

## Cách giải quyết
### Bước 1: 
- Mọi người tạo 1 file R (ví dụ `example.R`) chứa toàn bộ các scripts cần thiết để chạy được chức năng cần thiết và thiết lập để R lưu lại kết quả của mình sau khi đã thực thi thành công.
### Bước 2:
- Mọi người tạo 1 file bash script (ví dụ `execute.sh`) theo template:
```{bash}
#!/bin/sh
shopt -s expand_aliases

taskset -c <core_list> Rscript <R_path_file>
```
với `<core_list>` là danh sách các cores mà mọi người muốn sử dụng, ví dụ như mọi người cần request 3 cores, và mọi người check core thứ 3, 5 và 7 đang còn trống để chạy task của mình với file R ở trên thì mọi người sẽ thiết kế taskset như sau:
```{bash}
taskset -c 3,5,7 Rscript example.R
```
khi đó, taskset sẽ reserve 3 cores này chỉ phục vụ riêng cho tác vụ của mọi người và chỉ thực hiện trên các cores đã được khai báo và không ảnh hưởng đến các cores còn lại trong server.
