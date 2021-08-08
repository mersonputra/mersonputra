<?php
session_start();
$error = false;
if (isset($_SESSION['id_user'])) {
 header("location: index.php");
} else {
 $submit = @$_POST['submit'];
 $username = @$_POST['username'];
 $password = @$_POST['password'];
 $encodedPassword = md5($password);
 if (isset($submit)) {
 if($username == '' || $password == '') {
 $error = true;
 } else {
 include_once('./config/db.php');
 $query = "SELECT users.id, roles.nama_role FROM users ";
 $query .= "LEFT JOIN roles ON roles.id = users.id_role ";
 $query .= "WHERE username='$username' AND password='$encodedPassword'";
 $result = $connect->query($query);
 if ($result->num_rows > 0) {
 $user = $result->fetch_assoc();
 $id_user = $user['id'];
 $role = $user['nama_role'];
 $query = "SELECT nama_lengkap, status FROM biodata WHERE id_user = '$id_user'";
 $result = $connect->query($query);
 if ($result->num_rows > 0) {
 $biodata = $result->fetch_assoc();
 if ($biodata['status']) {
 $_SESSION['id_user'] = $id_user;
 $_SESSION['nama_lengkap'] = $biodata['nama_lengkap'];
 $_SESSION['role'] = $role;
 if ($role === 'mahasiswa') {
 $query = "SELECT prodi.nama_prodi FROM mahasiswa ";
 $query .= "LEFT JOIN prodi ON prodi.id = mahasiswa.id_prodi ";
 $query .= "WHERE id_user = '$id_user'";
 $result = $connect->query($query);
 if ($result->num_rows > 0) {
 $prodi = $result->fetch_assoc();
 $_SESSION['nama_prodi'] = $prodi['nama_prodi'];
 }
 }
 header("location: index.php");
 } else {
 $error = true;
 $errorText = "Akun ini tidak aktif, silahkan hubungi administrator";
 }
 }
 } else {
 $error = true;
 $errorText = "NIM atau password salah";
 }
 }
 }
}
?>
