## (1) Commit 1 Reflection

Secara singkat, fungsi handle_connection berguna untuk memproses permintaan klien, yaitu koneksi TCP yang masuk dari TcpListener.

---
``` rust
fn handle_connection(mut stream: TcpStream) {
```
Pada potongan kode di atas, dapat dilihat bahwa fungsi menerima parameter `TcpStream` yang mewakili koneksi aktif antara server dan klien. `mut` menunjukkan bahwa `stream` akan dimodifikasi dalam fungsi.

---
``` rust
let buf_reader = BufReader::new(&mut stream);
```
`BufReader` dibuat dari `stream` untuk membaca data per baris secara efisien. Selain itu, `BufReader` juga membungkus `TcpListener` agar lebih mudah membaca permintaan HTTP.

---
``` rust
let http_request: Vec<_> = buf_reader 
    .lines()
    .map(|result| result.unwrap()) 
    .take_while(|line| !line.is_empty()) 
    .collect();
```
- `buf_reader.lines()` menggunakan iterator untuk membaca koneksi per baris. 
- `.map(|result| result.unwrap())` melakukan ekstraksi string dari `Result<String, Error>`
- `.take_while(|line| !line.is_empty())` akan terus membaca semua baris sampai menemukan baris kosong.
- `.collect()` menyatukan semua baris yang tidak kosong ke dalam `Vec<String>`.

---
``` rust
println!("Request: {:#?}", http_request);
```
Baris permintaan HTTP yang dikumpulkan dicetak dalam format debug, untuk membantu proses debugging.

## (2) Commit 2 Reflection
Setelah dimodifikasi, fungsi `handle_connection` sekarang berguna untuk membaca permintaan HTTP dan merespons dengan file HTML. 

---
Modifikasi tambahan kode yang dilakukan:
``` rust
let status_line = "HTTP/1.1 200 OK";
let contents = fs::read_to_string("hello.html").unwrap();
let length = contents.len();
```
- `status_line = "HTTP/1.1 200 OK"` menentukan status HTTP untuk respons, yaitu `200 OK` berarti permintaan berhasil. 
- `fs::read_to_string("hello.html").unwrap()` membaca isi file hello.html, jika file tidak ditemukan maka `unwrap()` akan menyebabkan error dan program berhenti.
- `length = contents.len();` menyimpan panjang file dalam byte.
---
``` rust
let response =
    format!("{status_line}\r\nContent-Length:
{length}\r\n\r\n{contents}");
```
Untuk membentuk respons yang akan dikirim ke klien.

---
``` rust
stream.write_all(response.as_bytes()).unwrap();
```
Mengonversi respons menjadi byte dan mengirimkannya melalui `TcpStream`, sehingga dikirim kembali ke klien.
