Cấu trúc phần mềm nhanh

Usage: organize [options]

Options:
  -o, --output  Output directory - Creates one if doesn't exist         [string]
  -d, --date    Organize files by dates                                [boolean]
  -s, --source  Source directory to organize                 [string] [required]
  -t, --type    Specific types to organize - strings of file extensions  [array]
  -f, --folder  Specific folder to move specific files to               [string]
  -h, --help    Show help                                              [boolean]

Examples:
  organize -s ~/Downloads -o . -t mp3 wav -f "Songs"


Đơn giản hóa thì:
- -o: Tạo output, 
	- nếu để "." thì tự động chia trong thư mục đang làm việc
	- còn lại tạo thư mục dựa theo tên output
- -s: Tự động quét ở source được cho
- -h: Hiển thị thông báo gợi ý

functions

helper

1. is_valid_file? -> use `Walkdir` to list all file (TODO)
2. create a directory DONE
	-> there will be more than one type 
	-> 
3. check extension DONE
mondai: how do we check the extension of the file (There are more than 1 filetype list)

4. move files to the organized folders (TODO)


main 

parse arguments

source directory
output directory