Mình học được những gì từ dự án Bartib

Config file (Đây có thể là tệp Report) 

```rust
use chrono::Duration;

// Định dạng thời gian?
pub static FORMAT_DATETIME: &str = "%F %R";
pub static FORMAT_TIME: &str = "%R";
pub static FORMAT_DATE: &str = "%F";
pub static DEFAULT_WIDTH: usize = usize::MAX;
pub static REPORT_INDENTATION: usize = 4;

#[derive(Debug)]
pub struct ProcessConfig {
    pub round: Option<Duration>, // Thời lượng làm việc tổng
}
```

Ở `main.rs` thì
`fn main() -> Result<()>` -> Làm như thế này bằng `anyhow`

Còn khi thêm args sử dụng `clap` thì:

```rust
    let arg_time = Arg::with_name("time")
        .short("t")
        .long("time")
        .value_name("TIME") // Value name trong Clap dùng để làm gì
        .help("the time for changing the activity status (HH:MM)")
        .takes_value(true); //takes_value làm gì

    let arg_from_date = Arg::with_name("from_date")
        .long("from")
        .value_name("FROM_DATE")
        .help("begin of date range (inclusive)")
        .takes_value(true);
```

Còn có thể có các attr khác như

```rust
        .required(false) // Không bắt buộc
        .conflicts_with_all(&["from_date", "to_date"]) //?
```

Đây là một cách khá đỉnh để sử dụng Clap
```rust
    let matches = App::new("bartib") //Thông tin
        .version(crate_version!())
        .author("Nikolas Schmidt-Voigt <nikolas.schmidt-voigt@posteo.de>")
        .about("A simple timetracker")
        .after_help("To get help for a specific subcommand, run `bartib [SUBCOMMAND] --help`.
To get started, view the `start` help with `bartib start --help`")
        .setting(AppSettings::SubcommandRequiredElseHelp) // Setting trong Clap
        .setting(AppSettings::VersionlessSubcommands)
        .arg(
            Arg::with_name("file")
                .short("f")
                .value_name("FILE")
                .help("the file in which bartib tracks all the activities")
                .env("BARTIB_FILE")
                .takes_value(true),
        ) // Thêm một arg vào?
        .subcommand(
            SubCommand::with_name("start")
                .about("starts a new activity")
                .arg(arg_project.clone().required(true)) // Đây là thông tin mình đã lấy ở trên
                .arg(arg_description.clone().required(true)) // Như trên
                .arg(&arg_time), // Không có tag `required`
        )
		/// subcommand khác arg nha
		/// Subcommand sẽ là một lệnh đặc biệt và nó sẽ trông như thế này
		/// `bartib start (Đây là một subcommand) -s "" -p "" (Hai yêu cầu của lệnh)`
```

Xác định lỗi thiếu tệp:

```rust
    let file_name = matches.value_of("file")
        .context("Please specify a file with your activity log either as -f option or as BARTIB_FILE environment variable")?;

```

Tại sao lại vậy?

Ứng dụng cần một tệp plain text để quản lý dữ liệu của phần mềm, vậy nên nó CẦN biết vị trí để có thể cập nhật.

```rust
Arg::with_name("file")
                .short("f")
                .value_name("FILE")
                .help("the file in which bartib tracks all the activities")
                .env("BARTIB_FILE")
                .takes_value(true),
```

Ở đây lại có thêm `.env("BARTIB_FILE")` để lấy giá trị từ hệ điều hành (Hình như là Shell Variable, không biết gọi sao), nhưng mình có thể sử dụng để đỡ cần phải ghi tên tệp mỗi lần chạy phần mềm, vì mỗi lần chạy nó sẽ yêu cầu `-f` hoặc `--file`.

```bash
export BARTIB_FILE=activities.bartib
```

Cuối function `main()` thì có cái này:

```rust
run_subcommand(&matches, file_name)
```

Và đây là function gốc

```rust
fn run_subcommand(matches: &ArgMatches, file_name: &str) -> Result<()> {
    match matches.subcommand() {
        ("start", Some(sub_m)) => { // Some(sub_m) = giá trị được lấy từ câu lệnh
            let project_name = sub_m.value_of("project").unwrap();
            let activity_description = sub_m.value_of("description").unwrap();
            let time = get_time_argument_or_ignore(sub_m.value_of("time"), "-t/--time")
                .map(|t| Local::now().date_naive().and_time(t));

            bartib::controller::manipulation::start(
                file_name,
                project_name,
                activity_description,
                time,
            )
			// Đây là một chương trình trong tệp `manipulation.rs` thuộc module `controller`, sẽ xử lý sau.
        }
```


Xử lý giá trị thời gian

Thực ra đối với nhiều chương trình, mình nên hạn chế trả về giá trị chính xác của nó, ví dụ như `String` mà nên là `Result<String, std::io::Error>` nếu chương trình có thể phát sinh lỗi.

Hãy nhìn vào chương trình này trong Bartib

```rust
fn get_number_argument_or_ignore(
    number_argument: Option<&str>,
    argument_name: &str,
) -> Option<usize> {
    if let Some(number_string) = number_argument {
        let parsing_result = number_string.parse();

        if let Ok(number) = parsing_result {
            Some(number)
        } else {
            println!(
                "Can not parse \"{number_string}\" as number. Argument for {argument_name} is ignored"
            );
            None
        }
    } else {
        None
    }
}
```

Một số phần thắc mắc:

- Tại sao lại là usize? TODO: Tìm hiểu thêm về usize (Nếu nhớ không nhầm là một giá trị không xác định trong compile time, kiểu na ná str ấy. Nhưng không được phép trả về str vì vấn đề Lifetime - một khái niệm trong Rust nha :>)
- Học lại về `if let` đi nha. Tính đến thời điểm viết thì mình có thói quen dùng `if` hoặc `match` nhiều hơn là `if let`. `if let` để xử lý một số trường hợp sẽ rất tiện. Ví dụ như chỉ trả về giá trị không phải lỗi (Nếu được nên xử lý nó thông qua `else`)  


Thư viện xử lý thời gian - chrono - TODO: Cần học cơ bản.

