## (1) Apa yang di dalam `handle_connection` function

Secara singkat, fungsi handle_connection berguna untuk memproses permintaan klien, yaitu koneksi TCP yang masuk dari TcpListener.

``` rust
fn handle_connection(mut stream: TcpStream) {
```
Pada potongan kode di atas, dapat dilihat bahwa fungsi menerima parameter `TcpStream` yang mewakili koneksi aktif antara server dan klien. `mut` menunjukkan bahwa `stream` akan dimodifikasi dalam fungsi.

``` rust
let buf_reader = BufReader::new(&mut stream);
```
`BufReader` dibuat dari `stream` untuk membaca data per baris secara efisien. Selain itu, `BufReader` juga membungkus `TcpListener` agar lebih mudah membaca permintaan HTTP.

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

``` rust
println!("Request: {:#?}", http_request);
```
Baris permintaan HTTP yang dikumpulkan dicetak dalam format debug, untuk membantu proses debugging.

## (2) 