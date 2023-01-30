**1. Tạo một image mới (image được tạo theo cơ chế cache)**

``docker build -t {ten_nguoi_build}/{ten_du_an}``

``docker build -t linhai/media_activity``

**2. Tạo một container để chạy image**

``docker run --name {ten_container} {ten_image}``

``docker run --name media_activity linhai/media_activity``

**3. Xóa container**

``docker container rm {ten_container}``

``docker container rm media_activity``

**4. List các images**

``docker images``
