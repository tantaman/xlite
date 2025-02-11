# XLite - query Excel (.xlsx, .xls) and Open Document spreadsheets (.ods) as SQLite virtual tables

XLite is a SQLite extension written in Rust. The main purpose of this library is to allow working with spreadsheets from SQLite exposing them as [virtual tables](https://sqlite.org/vtab.html).

### Download

![build](https://github.com/x2bool/xlite/actions/workflows/build.yml/badge.svg)

The following prebuilt libraries are available for [download](https://github.com/x2bool/xlite/releases):

![release](https://img.shields.io/github/v/release/x2bool/xlite?display_name=release)

|  | Linux | Windows | MacOS |
|--|--|--|--|
| x86 | [libxlite.so.tar.gz](https://github.com/x2bool/xlite/releases/latest/download/libxlite-linux-x86.tar.gz)️ | [xlite.dll.zip](https://github.com/x2bool/xlite/releases/latest/download/xlite-windows-x86.zip)️ | N/A |
| x86-64 | [libxlite.so.tar.gz](https://github.com/x2bool/xlite/releases/latest/download/libxlite-linux-x64.tar.gz)️ | [xlite.dll.zip](https://github.com/x2bool/xlite/releases/latest/download/xlite-windows-x64.zip)️ | [libxlite.dylib.zip](https://github.com/x2bool/xlite/releases/latest/download/libxlite-macos-x64.zip) |
| AArch64 |   |   | [libxlite.dylib.zip](https://github.com/x2bool/xlite/releases/latest/download/libxlite-macos-aarch64.zip) |

Please contribute the missing build targets if you know how to implement them with GitHub Actions.

### How to build

```bash
cargo build --release
```

This step will produce `libxlite.so` or `libxlite.dylib` or `xlite.dll` depending on your operation system.

### How to use

Assuming you have sqlite3 command line tools installed, `libxlite` library in your current directory and some spreadsheet file on your disk you can load extension:

```bash
sqlite3 # will open SQLite CLI
> .load libxlite # or "xlite" on Windows
```

This will load `xlite` module, now it can be used to create virtual tables.

Creating a virtual table (this sample uses the .xslx file from the tests directory):

```sql
CREATE VIRTUAL TABLE test_data USING xlite(
    FILENAME './tests/abcdef.xlsx',
    WORKSHEET 'Sheet1',
    RANGE 'A2:F'
);
```

Explanation: this statement will create a virtual table based on the .xlsx file and the worksheet named "Sheet1".

Optional `RANGE` parameter is used here to skip the first row in the table. `A2:F` meaning is `use columns from A to F but start from 2nd row`.

Querying:

```sql
SELECT A, B, C, D, E, F from test_data;
```

Columns are named according to their name (index) in the spreadsheet.

```sql
SELECT COUNT(*), D FROM test_data GROUP BY D ORDER BY COUNT(*);
```

All operations supported by SQLite can be executed on spreadsheets as long as it is supported by the virtual table mechanism.

Dropping:

```sql
DROP TABLE test_data;
```

This statement will drop only the virtual table. Physical file won't be deleted.


### Limitations

`INSERT`, `UPDATE` and `DELETE` statements are not supported right now.

### About

This project is experimental, use at your own risk. The project is developed in my free time as a way to learn Rust and database systems.
