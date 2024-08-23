Mình học được những gì từ dự án Bartib

### Chia cấu trúc tệp
- Dù không hiểu lắm nhưng họ chia `Controller - Data - View` là sao?

### Tệp config (Đây có thể là tệp Report) 

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

### `main.rs`
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


### Sử dụng tag để xử lý error cùng `thiserror`
Thêm vào `Cargo.toml`
```toml
[dependencies]
thiserror = "1.0"
``` 

Hình như chỉ thường dùng trong `Struct` hoặc `Enum`

```rust
#[derive(Error, Debug)]
pub enum ActivityError {
    #[error("could not parse date or time of activity")]
    DateTimeParseError,
    #[error("could not parse activity")]
    GeneralParseError,
}
```

### Khi nào sử dụng `#[must_use]`
`#[must_use]`: Được dùng khi "The #[must_use] attribute can be applied to types or functions when failing to explicitly consider them or their output is almost certainly a bug." (Được lấy từ std-dev-guide)

`NaiveDateTime`: Được sử dụng không cần Timezone, xử lý thời gian thế nào.

### Một số các mẫu `unwrap()` khác
```rust
time.unwrap_or_else(|| Local::now().naive_local())
```
If the value is `None`, it will return Local::now().naive_local()`

```rust
or_else(|| Some(Local::now().naive_local())); // Nếu vẫn muốn trả về Option<>?
```
`signed_duration_since()` calculates the time it takes. Something like

```rust
	end.signed_duration_since(self.start)
```

### Đọc và ghi tệp
- Khi đọc tệp thì nên dùng `BufReader` thay vì `fs::read_to_string()`.

```rust
	let file_handler =
        File::open(file_name).context(format!("Could not read from file: {file_name}"))?;
    let reader = BufReader::new(file_handler);
    let lines = reader
        .lines()
        .map_while(Result::ok)
        .enumerate()
        .map(|(line_number, line)| Line::new(&line, line_number.saturating_add(1)))
        .collect();
```

Sử dụng `enumerate()` để trả giá trị kiểu `(line_number, line_content)`. Sẽ có ích trong một số trường hợp, chắc vậy.

Nhưng còn ghi tệp

```rust
pub fn write_to_file(file_name: &str, file_content: &[Line]) -> Result<(), io::Error> {
    let file_handler = get_bartib_file_writable(file_name)?;
	// Sử dụng một Enum để theo dõi trạng trái của dòng văn bản.
	// Nếu nó vẫn được để là Unchanged thì sẽ giữ nguyên
	// Còn không thì sửa đổi như bên dưới
    for line in file_content {
        match &line.status {
            LineStatus::Unchanged => {
                if let Some(plaintext) = &line.plaintext {
                    writeln!(&file_handler, "{plaintext}")?
                } else {
                    write!(&file_handler, "{}", line.activity.as_ref().unwrap())?
                }
            }
            LineStatus::Changed => write!(&file_handler, "{}", line.activity.as_ref().unwrap())?,
        }
    }
    Ok(())
}
```

Còn có thể đóng `OpenOptions` vào một cái khá hay như thế này

```rust
// create a write handle to a file
fn get_bartib_file_writable(file_name: &str) -> Result<File, io::Error> {
    OpenOptions::new()
        .create(true)
        .write(true)
        .truncate(true)
        .open(file_name)
}
```

### Đóng giá trị vào trong `&[value]` để làm gì?
```rust
pub fn write_to_file(file_name: &str, file_content: &[Line]) -> Result<(), io::Error> {
	Ok(())
}
```

### Sử dụng `usize` khác gì so với `u32`, `u64`? 



### Tạo một custom Error class
```rust
#[derive(Error, Debug)]
pub enum ActivityError {
    #[error("could not parse date or time of activity")] // Lấy từ `thiserror` crate
    DateTimeParseError,
    #[error("could not parse activity")]
    GeneralParseError,
}
```
### Thực hiện impl các chức năng từ `std` lib cho các function hoặc struct riêng của mình?
```rust
impl fmt::Display for Activity {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
```

### Sử dụng `as-ref()` khác gì so với `&`?
```rust
write!(&file_handler, "{}", line.activity.as_ref().unwrap())?
```
Theo docs thì: "Converts this type into a shared reference of the (usually inferred) input type." (as_ref()). Vậy tức là ở đây chỉ dừng ở mức `convert` chứ không thực sự `Borrow`?


## Viết chức năng lọc nội dung theo ngày, tuần, tháng
```rust
pub struct Filters {}

impl Filters {
```

Và các chức năng sẽ trông như thế này khi `impl`

```rust
pub fn active(activity: &&Activity) -> bool {
        !activity.is_stopped() // Chắc là sẽ chạy dùng `.filter()` và giá trị nếu trả true thì lấy, kiểu vậy
    }
	// NaiveDate:
    pub fn today(today: NaiveDate) -> impl Fn(&&Activity) -> bool {
        move |activity: &&Activity| activity.start.date() == today
    }
	// Tại sao lại `-> impl Fn(&&Activity)`? Dù đã từng dùng nhưng chưa bao giờ trả như vậy
	// Có phải giá trị của nó sẽ là `impl Fn(&&Activity) -> bool` (Cả cục?)
    pub fn current_week(today: NaiveDate) -> impl Fn(&&Activity) -> bool {
        let from_date = today - Duration::days(i64::from(today.weekday().num_days_from_monday()));
        let to_date = today;
        move |activity: &&Activity| {
            activity.start.date() >= from_date && activity.start.date() <= to_date
        }
    }
	// Trả giá trị theo tháng
	// `from_ymd_opt()`??
    pub fn current_month(today: NaiveDate) -> impl Fn(&&Activity) -> bool {
        let from_date = NaiveDate::from_ymd_opt(today.year(), today.month(), 1).unwrap();
        let to_date = today;
        move |activity: &&Activity| {
            activity.start.date() >= from_date && activity.start.date() <= to_date
        }
    }
```

### Sử dụng wildmatch để xử lý String

Không biết nhưng thấy người ta dùng và đọc trên [crates.io](https://crates.io/crates/wildmatch) thì thấy nó nhanh phết và chỉ dựa trên `stdlib`.

**Simple string matching with single- and multi-character wildcard operator.**

### Tạo kiểu lỗi riêng (Không hẳn)
Mình có thể giới hạn kiểu lỗi (Kiểu ví dụ chương trình chỉ có thể trả ra một số giá trị lỗi nhất định thì)

```rust
    pub activity: Result<activity::Activity, activity::ActivityError>,
```

### Về việc Test chương trình
Ở tệp `filter.rs`, để kiểm tra xem các hàm đã hoạt động đúng chưa bằng cách viết test. Có môt số cách hay mới tìm được là:

Sử dụng một hàm làm giá trị định sẵn. Tức là
```rust
    fn data() -> Vec<Activity> {
        let a0 = activity::Activity {
            project: "p1".to_string(),
            description: "d0".to_string(),
            start: date(2024, 2, 11),
            end: Some(date(2024, 2, 11) + Duration::hours(2)),
        };
        let a1 = activity::Activity {
            project: "p1".to_string(),
            description: "d1".to_string(),
            start: date(2024, 3, 11),
            end: Some(date(2024, 3, 11) + Duration::hours(2)),
        };
	.... // Có nhiều hơn nhưng đã lược bớt
	return vec![a0, a1, a2, a3, a4];
}
// Chuyển đổi cho nhanh để test. đỉnh của chóp
fn date(year: i32, month: u32, day: u32) -> NaiveDateTime {
        let date = NaiveDate::from_ymd_opt(year, month, day).unwrap();
        return NaiveDateTime::new(date, chrono::NaiveTime::from_hms_opt(10, 0, 0).unwrap());
    }
``` 

Thử cho `today`

```rust
#[test]
    fn filter_today() {
        let now = date(2024, 3, 19);
        let activities = data();
        let res: Vec<&Activity> = activities
            .iter()
            .filter(Filters::today(now.date()))
            .collect();
        assert_eq!(res.len(), 2);
        assert_eq!(res.first().unwrap().description.as_str(), "d3");
    }
```

### Lọc dữ liệu thông qua các nội dung khác như Description hay linh tinh
Ở đây tác giả sử dụng khá nhiều Lifetime, tính đến thời điểm hiện tại thì mình vẫn chưa dùng nhiều lắm.

Ở đây sẽ là nội dung lấy từ tệp `getter.rs`

function `get_activies()` là nền tảng để xử lý các phần khác, như lọc theo thứ tự tên, tính theo thời gian hoặc các thứ.

```rust
pub fn get_activities(
    file_content: &[bartib_file::Line], // Kiểu giá trị Line từ tệp bartib_file.rs
) -> impl Iterator<Item = &activity::Activity> { // impl Iterator<CustomItem>? tại sao
    file_content
        .iter()
		// .filter_map(): The returned iterator yields only the values for which the supplied closure returns Some(value).
        .filter_map(|line: &bartib_file::Line| match &line.activity { // Phòng TH lỗi 
            Ok(activity) => Some(activity),
            Err(_) => {
                println!(
                    "Warning: Ignoring line {}. Please see `bartib check` for further information",
                    line.line_number.unwrap_or(0),
                );
                None
            }
        })
}
```

### Làm tròn ngày

```rust
// Utility functions for rounding datetimes.
// Limitations:
// - Cannot handle days properly.
// - Does not consider DST changes, so rounding to hours may be off by an hour.
// - Does not consider leap seconds.
pub fn round_datetime(
    datetime: &chrono::NaiveDateTime,
    round: &chrono::Duration,
) -> chrono::NaiveDateTime {
    let timestamp = datetime.timestamp();
    let round_seconds = round.num_seconds();

    let rounded_timestamp =
        (timestamp as f64 / round_seconds as f64).round() as i64 * round_seconds;

    chrono::NaiveDateTime::from_timestamp_opt(rounded_timestamp, 0).unwrap()
}
```

Một số nhắc nhỏ:

Khi test thì ổng hay sử dụng `and_hms_opt(13, 30, 0)`(Makes a new NaiveDateTime from the current date, hour, minute and second) còn có cả `from_ymd_opt` (Makes a new NaiveDate from the calendar date (year, month and day)).

Khái niệm về NaiveDateTime vẫn còn khá mù mờ, chỉ biết là nó không liên quan đến Timezone khi làm việc với `Time` trong lập trình.

