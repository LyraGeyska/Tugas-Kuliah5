# Aplikasi CRUD Data Mahasiswa Dengan Menggunakan Jawa Swing dan Database PostgreSQL

## Deskripsi Penugasan
Pada Penugasan kelima Pemrograman Berbasis Objek (PBO) ini, yaitu mengimplementasikan penggunaan CRUD pada Netbeans menggunakan bahasa pemrograman Java dan database PostgreSQL. Penggunaan CRUD kali ini akan dibangun dengan bantuan Java Swing untuk mendukung operasi CRUD tersebut.

## Koneksi Database
Aplikasi ini nantinya akan terhubung ke database menggunakan JDBC, dengan library PostgreSQL yang diperlukan untuk mengelola operasi CRUD. Jadi pastikan anda telah membuat database terlebih dahulu menggunakan PostgreSQL dengan struktur yang sesuai dan jelas. Seperti yang digunakan pada projek kali ini yakni nim, nama_mahasiswa, alamat dan no_telepon.

## Connecting to Postgres With Java using JDBC
Berikut adalah code java untuk menghubungkan dabatabe PostgreSQL menggunakan JDBC :
<pre>
  Connection conn;
    Statement stmt;
    PreparedStatement pstmt;
    
    String driver = "org.postgresql.Driver";
    String koneksi = "jdbc:postgresql://localhost:5432/nama_database";
    String user = "postgres";
    String password = "******";
    
    InputStreamReader inputStreamReader = new InputStreamReader(System.in);
    BufferedReader input = new BufferedReader(inputStreamReader);
</pre>

## Pembuatan User Inferface GUI menggunakan Java Swing
Pada pembuatan antarmuka kali ini, akan menggunakan beberapa fitur Swing pada Java Swing, yakni seperti *Label* yang akan digunakan untuk membuat teks (Data Mahasiswa, NIM, NAMA, ALAMAT, NO TELEPON), *Text Field* untuk membuat field data (txtNim, txtNama, txtAlamat, txtTelepon), *Button* untuk membuat tombol pengoperasian CRUD (btnBaru, btnSimpan, btnUpdate, btnHapus, btnExit), *Table* untuk membuat tabel yang nantinya akan digunakan untuk menampilkan data (tblHasil) dengan menambahkan *Event MouseClicked* (tblHasilMouseClicked) yang berfungsi pada saat data dalam tabel dipilih, secara otomatis data akan terinput pada *Text Field* yang sudah dibuat dan siap untuk diperasikan lebih lanjut sesuai dengan data yang telah dipilih.

## Pembuatan Tampilan Utama
Untuk tampilan UI awal membutuhkan fungsi *tampil()*, maka dari itu penting halnya untuk mendahulukan bagian terpenting ini. Nantinya fungsi ini akan digunakan pada setiap awal membuat Aplikasi.
<pre>
  public void tampil() {
        try {
            // TODO code application logi
            Class.forName(driver);
            String sql = "SELECT * FROM mahasiswa";
            conn = DriverManager.getConnection(koneksi, user, password);
            stmt = conn.createStatement();
            
            while (!conn.isClosed()) {
                
                ResultSet rs = stmt.executeQuery(sql);
                this.tblHasil.setModel(pertemuankelima.DbUtils.resultSetToTableModel(rs));
                while (rs.next()) {
                    System.out.println(String.valueOf(rs.getObject(1)) + " "
                            + String.valueOf(rs.getObject(2)) + " "
                            + String.valueOf(rs.getObject(3)) + " " + String.valueOf(rs.getObject(4)));
                }
                conn.close();
            }
            
            stmt.close();
            
        } catch (ClassNotFoundException ex) {
            Logger.getLogger(BayuFrame.class.getName()).log(Level.SEVERE, null, ex);
        } catch (SQLException ex) {
            Logger.getLogger(BayuFrame.class.getName()).log(Level.SEVERE, null, ex);
        }
</pre>
Disini saya menambahkan *.setEnnable(Bolean)* untuk mematikan fungsi *Button* (btnSimpan) dan *Text Field* (txtNim txtNama, Txt Alamat, txtTelepon). Karena memungkinkan tidak adanya penambahan data sebelum *Button* (btnBaru) di klik. Berikut adalah codenya :
<pre>
  btnSimpan.setEnabled(false);
        txtNim.setEnabled(false);
        txtNama.setEnabled(false);
        txtAlamat.setEnabled(false);
        txtTelepon.setEnabled(false);
    }
</pre>
!! KODE DIATAS MERUPAKAN CODE SATU-KESATUAN, HANYA SAJA DIPISAHKAN OLEH TEXT SETELAH CODE PERTAMA.

## Penggunakan Fungsi btnBaru
Pada *Button* ini, adalah salah satu button yang aktif setelah button exit saat Aplikasi pertama kali dijalankan. Button ini digunakan untuk membuat data baru, karena sebelum button ini di klik *Text Field* untuk pengisian data dinonaktifkan sementara. Berikut adalah code dari *btnBaru* :
<pre>
  private void btnBaruActionPerformed(java.awt.event.ActionEvent evt) {                                        
        // TODO add your handling code here:
        txtNim.setText("");
        txtNama.setText("");
        txtAlamat.setText("");
        txtTelepon.setText("");
        
        btnUpdate.setEnabled(false);
        btnHapus.setEnabled(false);
        btnSimpan.setEnabled(true);
        txtNim.setEnabled(true);
        txtNama.setEnabled(true);
        txtAlamat.setEnabled(true);
        txtTelepon.setEnabled(true);
    }
</pre>

## Penggunaaan Fungsi btnSimpan
Pada *Button* Simpan (btnSimpan) disini, akan difungsikan sebagai tombol untuk menyimpan atau menambahkan data baru kedalam database, Berikut adalah code untuk *btnSimpan* :
<pre>
  private void btnSimpanActionPerformed(java.awt.event.ActionEvent evt) {                                          
        // TODO add your handling code here:
        if (txtNim.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (txtNama.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (txtAlamat.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (txtTelepon.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else {
            try {
                Class.forName(driver);
                conn = DriverManager.getConnection(koneksi, user, password);
                conn.setAutoCommit(false);
                
                String sql = "INSERT INTO mahasiswa VALUES(?,?,?,?)";
                pstmt = conn.prepareStatement(sql);
                
                String nim, nama, alamat, no_telepon;
                nim = txtNim.getText();
                nama = txtNama.getText();
                alamat = txtAlamat.getText();
                no_telepon = txtTelepon.getText();
                
                pstmt.setLong(1, Long.parseLong(nim));
                pstmt.setString(2, nama);
                pstmt.setString(3, alamat); // Pastikan formatnya benar
                pstmt.setLong(4, Long.parseLong(no_telepon));
                
                pstmt.executeUpdate();
                conn.commit();
                pstmt.close();
                conn.close();
                bersih();
                
                JOptionPane.showMessageDialog(null, "Data Berhasil Ditambahkan");
            } catch (ClassNotFoundException | SQLException ex) {
                JOptionPane.showMessageDialog(null, "Terjadi Kesalahan Saat Pengisian Data");
            }
        }
        tampil();
    }      
</pre>
Code *Button* Simpan diatas akan dijalankan apabila *Text Field* telah terisi sesuai dengan strukturnya dan kemudian memilih tombol Simpan untuk menambahkan data baru ke dalam database. Setelah data berhasil ditambahkan, data akan otomatis ditambahkan dan ditampilkan pada *tblHasil*.

## Penggunaaan Fungsi btnUpdate
Pada *Button* Update (btnUpdate) disini, akan difungsikan sebagai tombol untuk merubah isi data yang ada dalam database. Fungsi ini akan dijalankan ketika data pada *Tabel* (tblHasil) dipilih. Berikut adalah code untuk *btnUpdate* :
<pre>
  String nim, nama, alamat, no_telepon;
        if (txtNim.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (txtNama.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (txtAlamat.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (txtTelepon.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else {
            try {
                Class.forName(driver);
                String sql = "UPDATE mahasiswa SET nama = ?, alamat = ?, no_telepon = ? WHERE nim = ?";
                conn = DriverManager.getConnection(koneksi, user, password);
                pstmt = conn.prepareStatement(sql);
                
                nim = txtNim.getText();
                nama = txtNama.getText();
                alamat = txtAlamat.getText();
                no_telepon = txtTelepon.getText();
                
                pstmt.setString(1, nama);
                pstmt.setString(2, alamat);
                pstmt.setLong(3, Long.parseLong(no_telepon));
                pstmt.setLong(4, Long.parseLong(nim));
                
                int rowsAffected = pstmt.executeUpdate();
                if (rowsAffected > 0) {
                    JOptionPane.showMessageDialog(null, "Data Berhasil Diupdate");
                    pstmt.close();
                    conn.close();
                    bersih();
                } else {
                    JOptionPane.showMessageDialog(null, "Data Tidak Ditemukan");
                }
            } catch (ClassNotFoundException | SQLException ex) {
                Logger.getLogger(CobaMahasiswa.class.getName()).log(Level.SEVERE, null, ex);
            }
        }
        tampil();
    }                             
</pre>
Pada code diatas, sebelumnya lebih baik membuat fungsi baru dan beri nama bersih() untuk menghapus *Text Field* atau mengosongkan ulang data yang ada pada *Text Field*, berikut adalah code nya :
<pre>
  public void bersih() {
        txtNim.setText("");
        txtNama.setText("");
        txtAlamat.setText("");
        txtTelepon.setText("");
    }
</pre>
Kemudian panggil fungsi bersih() yang sudah dibuat pada fungsi btnUpdate, letakkan pada baris dibawah seperti code btnUpdate diatas :
<pre>
  if (rowsAffected > 0) {
        JOptionPane.showMessageDialog(null, "Data Berhasil Diupdate");
        pstmt.close();
        conn.close();
        bersih();
    } else {
        JOptionPane.showMessageDialog(null, "Data Tidak Ditemukan");
  }
</pre>

## Penggunaan Fungsi btnHapus
Pada *Button* Hapus (btnHapus) disini, akan difungsikan sebagai tombol untuk menghapus data yang ada dalam database. Fungsi ini akan dijalankan ketika data pada *Tabel* (tblHasil) dipilih dan akan menghapus data berdasarkan Nim data yang dipilih. Berikut adalah code untuk *btnHapus* :
<pre>
  String nim;
        nim = txtNim.getText();
        
        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);
            
            int jawab = JOptionPane.showConfirmDialog(null, "Apakah Anda Yakin Akan Menghapus Data?");
            switch (jawab) {
                case JOptionPane.YES_OPTION:
                    String deleteSql = "DELETE FROM mahasiswa WHERE nim = ?";
                    pstmt = conn.prepareStatement(deleteSql);
                    pstmt.setLong(1, Long.parseLong(nim));
                    pstmt.executeUpdate();
                    pstmt.close();
                    conn.close();
                    bersih();
                    break;
                case JOptionPane.NO_OPTION:
                    JOptionPane.showMessageDialog(this, "Data Tidak Jadi Dihapus");
                    break;
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Periksa Kembali");
        }
        tampil();
    }                                        
</pre>
Seperti halnya btnUpdate, pada btnHapus ini menambahkan fungsi bersih() untuk membersihkan *Text Field* yang sebelumnya terisi data yang telah dipilih.

## Penggunaan Fungsi btnExit
<pre>
  *Button* ini btnExit ditambahkan untuk keluar dari Aplikasi hanya dengan memilih tombol ini, tanpa harus close program melalui x. Berikut adalah codenya :
private void btnExitActionPerformed(java.awt.event.ActionEvent evt) {                                        
        // TODO add your handling code here:
        System.exit(0);
    } 
</pre>

#DBUtils Class
Kelas DbUtils ini digunakan untuk mengkonversi data dari ResultSet menjadi model tabel (TableModel) yang dapat ditampilkan pada komponen tabel di Java Swing. Metode resultSetToTableModel mengambil metadata kolom dari ResultSet, mendapatkan nama-nama kolom, dan mengisi baris-baris tabel dengan data dari database, lalu menampilkannya dalam bentuk DefaultTableModel.

Semoga penjelasan diatas mudah dan  dapat dipahami dengan baik, Terima Kasih
