just some quick note

## Set up config file
most people use serde, so I will go with that

1. set CONFIG_FILE_NAME as const value.
```rust
const CONFIG_FILE_NAME: &str = "config.json";
```


Fetch from the config file

```rust
#[derive(Serialize, Deserialize, Default)]
struct Config {
    repo: PathBuf,
}

```
here is the thing, remember that `Serialize, Deserialize` should be added by default. it's required to parse the config code/


`trait` in rust helps creating predefined processes (kinda like `interface` in other programming languages but there're some subtle differences which the book has pointed out, will refer to it later).


`create_config_dir()`

```rust
fn config_dir_create(&self) -> io::Result<()> {
        self.config_dir_path().and_then(fs::create_dir_all) // wdym by `and_then`
    }
```

for and_then 

Calls op if the result is Ok, otherwise returns the Err value of self. This function can be used for control flow based on Result values.

it;s like if it's failed to config the config dir it will create a new one or sthm

fact: impl Eq, PartialEq allows using == (it's pretty useful when it comes to compare the two objects of something)


```rust
fn config_dir_exists(&self) -> bool {
        self.config_dir_path().and_then(fs::metadata).is_ok()
    }
```
Metadata information about a file

the program is working with git by default (that's not my cup of tea so not for now).


## The whole struct for the program

```rust 
pub struct Eureka<
    CM: ConfigManagement,
    W: Print + PrintColor,
    R: ReadInput,
    G: GitManagement,
    PO: ProgramOpener,
> {
    cm: CM,
    printer: W,
    reader: R,
    git: G,
    program_opener: PO,
}
```
idk if this should  be considered `Generic` but here's the thing:
- [by example](https://doc.rust-lang.org/rust-by-example/generics.html)
- [from some1 from reddit](https://www.reddit.com/r/rust/comments/17k29a9/can_please_someone_explain_me_when_and_where_to/)

this looks weird, this is how it was implemented 

```rust
impl<CM, W, R, G, PO> Eureka<CM, W, R, G, PO>
where
    CM: ConfigManagement,
    W: Print + PrintColor,
    R: ReadInput,
    G: GitManagement,
    PO: ProgramOpener,
{
    pub fn new(cm: CM, printer: W, reader: R, git: G, program_opener: PO) -> Self {
        Eureka {
            cm,
            printer,
            reader,
            git,
            program_opener,
        }
    }
```

I love how this program works with other programs, in SHELL or sth. Here is how it was done


```rust
pub trait ProgramOpener {
    fn open_editor(&self, file_path: &str) -> io::Result<()>;
    fn open_pager(&self, file_path: &str) -> io::Result<()>;
}
```

using `trait` makes it more convenient to impl those functions into another Struct or something?

```rust
impl ProgramOpener for ProgramAccess {
    fn open_editor(&self, file_path: &str) -> io::Result<()> {
        self.open_with_fallback(file_path, "EDITOR", "vi")
    }

    fn open_pager(&self, file_path: &str) -> io::Result<()> {
        self.open_with_fallback(file_path, "PAGER", "less")
    }
}
```

here is the ProgramAccess struct

```rust
impl ProgramAccess {
    fn open_with_fallback(&self, file_path: &str, env_var: &str, fallback: &str) -> io::Result<()> {
        let program = env::var(env_var)
            .map(PathBuf::from)
            .or_else(|_| self.get_if_available(fallback))?;

        // Make sure file exists
        fs::metadata(file_path)?;
        Command::new(program).arg(file_path).status().map(|_| ())
    }

    fn get_if_available(&self, program: &str) -> io::Result<PathBuf> {
        which::which(program).map_err(|err| std::io::Error::new(ErrorKind::NotFound, err))
    }
}
```

learn the testing later, ahh I guess I should learn about debugger (later, just a todo)

The `reader.rs` file


```rust
use std::io;

pub trait ReadInput {
    fn read_input(&mut self) -> io::Result<String>;
}

pub struct Reader<R> {
    reader: R,
}

impl<R> Reader<R> {
    pub fn new(reader: R) -> Self {
        Self { reader }
    }
}

impl<R: io::BufRead> ReadInput for Reader<R> {
    fn read_input(&mut self) -> io::Result<String> {
        let mut input = String::new();
        self.reader.read_line(&mut input)?;
        Ok(input.trim().to_string())
    }
}

#[allow(non_snake_case)]
#[cfg(test)]
mod tests {
    use crate::reader::{ReadInput, Reader};

    #[test]
    fn test_reader__read_input__success() {
        let input = b"  my input with whitespace chars  ";
        let mut reader = Reader::new(&input[..]);

        let actual = reader.read_input().unwrap();
        let expected = "my input with whitespace chars".to_string();

        assert_eq!(actual, expected);
    }
}
```

how it takes the input from the user (I have no idea how it works atm)