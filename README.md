(Tietokannan muokkaaminen)
<?php
mysqli_report(MYSQLI_REPORT_ALL ^ MYSQLI_REPORT_INDEX);

// Avataan yhteys tietokantaan
$yhteys = mysqli_connect("localhost", "trtkm25b3_4", "FdkqeULM", "trtkm25b3_4");
if (!$yhteys){
    die("Yhteysvirhe: " . mysqli_connect_error());
}

// Jos lomake on lähetetty päivitetään aihe ja viesti tietokantaan
if (isset($_POST["id"]) && !empty($_POST["id"])) {

    // Haetaan lomakkeelta lähetetyt arvot
    $id = (int)$_POST["id"];
    $aihe = isset($_POST["aihe"]) ? $_POST["aihe"] : "";
    $viesti = isset($_POST["viesti"]) ? $_POST["viesti"] : "";

    // Päivitetään tietokantaan vain se rivi, jonka id vastaa lomakkeelta saatua id:tä
    $sql = "UPDATE trtkm25b3_4_yhteydenottolomake SET aihe=?, viesti=? WHERE id=?";
    $stmt = mysqli_prepare($yhteys, $sql);
    mysqli_stmt_bind_param($stmt, "ssi", $aihe, $viesti, $id);
    mysqli_stmt_execute($stmt);

    mysqli_stmt_close($stmt);
    mysqli_close($yhteys);

    // Tallennuksen jälkeen palataan takaisin viestilistaan
    header("Location: /~trtkm25b_4/admin/index.php");
    exit;
}

// Haetaan tietokannasta viesti, jota käyttäjä haluaa muokata
$muokattava = isset($_GET["muokattava"]) ? (int)$_GET["muokattava"] : 0;

// Jos id ei ole kelvollinen palataan takaisin listaan
if ($muokattava <= 0){
    mysqli_close($yhteys);
    header("Location: /~trtkm25b_4/admin/index.php");
    exit;
}

// Haetaan tietokannasta muokattavan viestin nykyiset tiedot
$sql = "SELECT id, aihe, viesti FROM trtkm25b3_4_yhteydenottolomake WHERE id=?";
$stmt = mysqli_prepare($yhteys, $sql);
mysqli_stmt_bind_param($stmt, "i", $muokattava);
mysqli_stmt_execute($stmt);

$tulos = mysqli_stmt_get_result($stmt);
$rivi = mysqli_fetch_assoc($tulos);

mysqli_stmt_close($stmt);
mysqli_close($yhteys);

// Lopetetaan suoritus virheilmoitukseen, jos viestiä ei löytynyt
if (!$rivi){
    die("Tietuetta ei löytynyt");
}
?>

<!doctype html>
<html lang="fi">
<head>
<meta charset="utf-8">
<title>Muokkaa viestiä</title>
</head>
<body>

<h2>Muokkaa viestiä</h2>
<!-- Lomake viestin muokkaamiseen -->
<form method="post" action="">
  <input type="hidden" name="id" value="<?php echo (int)$rivi["id"]; ?>">
  
<!-- Aihekenttä -->
  Aihe:<br>
  <input type="text" name="aihe" value="<?php echo htmlspecialchars($rivi["aihe"]); ?>"><br><br>

  <!-- Viestikenttä -->
  Viesti:<br>
  <textarea name="viesti" rows="6" cols="60"><?php echo htmlspecialchars($rivi["viesti"]); ?></textarea><br><br>

    <!-- Tallennuspainike -->
  <input type="submit" value="Tallenna">

  <!-- Peruuta linkki takaisin viestilistaan -->
  <a href="/~trtkm25b_4/admin/index.php" style="margin-left:10px;">Peruuta</a>
</form>



(Tietokannasta poisto)
<?php
mysqli_report(MYSQLI_REPORT_ALL ^ MYSQLI_REPORT_INDEX);

try{

// Avataan yhteys tietokantaan
    $yhteys = mysqli_connect("localhost", "trtkm25b3_4", "FdkqeULM", "trtkm25b3_4");
}
catch(Exception $e){
    die("Yhteysvirhe");
}

// Haetaan poistettavan viestin id
$poistettava = isset($_GET["poistettava"]) ? (int)$_GET["poistettava"] : 0;

// Poistetaan vastaava rivi tietokannasta, jos id on annettu
if ($poistettava > 0){
    $sql = "DELETE FROM trtkm25b3_4_yhteydenottolomake WHERE id=?";
    $stmt = mysqli_prepare($yhteys, $sql);
    mysqli_stmt_bind_param($stmt, "i", $poistettava);
    mysqli_stmt_execute($stmt);
}

// Suljetaan tietokantayhteys ja palataan takaisin viestilistaan
mysqli_close($yhteys);
header("Location: /~trtkm25b_4/admin/index.php");
exit;
?>


</body>
</html>
