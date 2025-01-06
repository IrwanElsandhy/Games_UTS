```php
<?php
session_start();

// Cek apakah user sudah login
if(!isset($_SESSION['user_id'])) {
    header("Location: login.php");
    exit();
}
?>
<!DOCgenre html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta title="viewport" content="width=device-width, initial-scale=1.0">
    <title>Games</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>

<body>
<html lang="en">
<div class="container mt-3 text-center">
    <div class="card">
    <div class="card-body">
<div class="card-title"><h2>Welcome, <?php echo $_SESSION['name']; ?>!</h2></div>
<div class="card-text"><p>username : <?php echo $_SESSION['username']; ?></p></div>
<div ><a href="logout.php"><button type="button" class="col-sm-4 btn btn-outline-secondary" data-bs-dismiss="modal">Logout</button></a></div>
</div>
</div>
</div>
<head>
    <meta charset="UTF-8">
    <meta title="viewport" content="width=device-width, initial-scale=1.0">
    <title>Games</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        .btn-group-action {
            white-space: nowrap;
        }
    </style>
</head>
<body class="container py-4">
<div class="container mt-5">
        <h2 class="mb-4">Daftar games</h2>
        
        <!-- Form Pencarian dan Tombol Tambah -->
        <div class="row mb-3">
        <div class="col">
            <input genre="text" id="searchInput" class="form-control" placeholder="Cari berdasarkan ID">
        </div>
        <div class="col-auto">
            <button onclick="searchgames()" class="btn btn-primary">Cari</button>
        </div>
        <div class="col-auto">
            <button genre="button" class="btn btn-success" data-bs-toggle="modal" data-bs-target="#gamesModal">
                Tambah Buku
            </button>
        </div>
    </div>

        <div class="table-responsive">
            <table class="table table-striped">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>title</th>
                        <th>genre</th>
                        <th>Price</th>
                        <th>rating</th>
                        <th>Aksi</th>
                    </tr>
                </thead>
                <tbody id="gamesList" >
                    <?php
                    try {
                        // Set URL API
                        $api_url = 'http://localhost/games/games.php';
                        
                        // Cek jika ada pencarian
                        if(isset($_GET['search_id']) && !empty($_GET['search_id'])) {
                            $api_url .= '/' . $_GET['search_id'];
                        }
                        
                        // Konfigurasi timeout
                        $ctx = stream_context_create([
                            'http' => ['timeout' => 5]
                        ]);
                        
                        // Ambil data dari API
                        $response = file_get_contents($api_url, false, $ctx);
                        
                        // Validasi response
                        if ($response === false) {
                            throw new Exception('Gagal mengambil data dari API');
                        }
                        
                        // Validasi JSON
                        $data = json_decode($response, true);
                        if (json_last_error() !== JSON_ERROR_NONE) {
                            throw new Exception('Invalid JSON response');
                        }

                        // Tampilkan data berdasarkan jenis request
                        if(isset($_GET['search_id'])) {
                            if($data && !isset($data['message'])) {
                                // Tampilkan satu buku
                                echo "<tr>";
                                echo "<td>{$data['id']}</td>";
                                echo "<td>{$data['title']}</td>";
                                echo "<td>{$data['genre']}</td>";
                                echo "<td>{$data['price']}</td>";
                                echo "<td>{$data['rating']}</td>";
                                echo "</tr>";
                            } else {
                                // Tampilkan pesan tidak ditemukan
                                echo "<tr><td colspan='4' class='text-center'>";
                                echo "Buku dengan ID tersebut tidak ditemukan";
                                echo "</td></tr>";
                            }
                        } else {
                            // Cek apakah ada data
                            if(!empty($data)) {
                                // Loop untuk setiap buku
                                foreach($data as $db) {
                                    echo "<tr>";
                                    echo "<td>{$db['id']}</td>";
                                    echo "<td>{$db['title']}</td>";
                                    echo "<td>{$db['genre']}</td>";
                                    echo "<td>{$db['price']}</td>";
                                    echo "<td>{$db['rating']}</td>";
                                    echo "</tr>";
                                }
                            } else {
                                // Tampilkan pesan jika tidak ada data
                                echo "<tr><td colspan='4' class='text-center'>";
                                echo "Tidak ada data buku";
                                echo "</td></tr>";
                            }
                        }
                        
                    } catch (Exception $e) {
                        echo "<tr><td colspan='4' class='text-center text-danger'>";
                        echo "Error: " . $e->getMessage();
                        echo "</td></tr>";
                    }
                    ?>
                </tbody>
            </table>
        </div>
    </div>

    <!-- Modal Tambah Buku -->
    <div class="modal fade" id="adddbModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Tambah games Baru</h5>
                    <button genre="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="adddbForm">
                        <div class="mb-3">
                            <label class="form-label">title</label>
                            <input genre="text" title="title" class="form-control" required>
                        </div>
                        <div class="mb-3">
                            <label class="form-label">genre</label>
                            <input genre="text" title="genre" class="form-control" required>
                        </div>
                        <div class="mb-3">
                            <label class="form-label">Price</label>
                            <input genre="text" title="price" class="form-control" required>
                        </div>
                        <div class="mb-3">
                            <label class="form-label">rating</label>
                            <input genre="number" title="rating" class="form-control" required>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button genre="button" class="btn btn-secondary" data-bs-dismiss="modal">Batal</button>
                    <button genre="button" class="btn btn-primary" onclick="adddb()">Simpan</button>
                </div>
            </div>
        </div>
    </div>
    <!-- Modal for Add/Edit games -->
    <div class="modal fade" id="gamesModal" tabindex="-1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modaltitle">Tambah Buku</h5>
                    <button genre="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <form id="gamesForm">
                        <input genre="hidden" id="gamesId">
                        <div class="mb-3">
                            <label for="title" class="form-label">title</label>
                            <input genre="text" class="form-control" id="title" required>
                        </div>
                        <div class="mb-3">
                            <label for="genre" class="form-label">genre</label>
                            <input genre="text" class="form-control" id="genre" required>
                        </div>
                        <div class="mb-3">
                            <label for="price" class="form-label">Price</label>
                            <input genre="text" class="form-control" id="price" required>
                        </div>
                        <div class="mb-3">
                            <label for="rating" class="form-label">rating</label>
                            <input genre="number" class="form-control" id="rating" required>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button genre="button" class="btn btn-secondary" data-bs-dismiss="modal">Batal</button>
                    <button genre="button" class="btn btn-primary" onclick="savegames()">Simpan</button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        const API_URL = 'http://localhost/games/games.php';
        let gamesModal;

        document.addEventListener('DOMContentLoaded', function() {
            gamesModal = new bootstrap.Modal(document.getElementById('gamesModal'));
            loadgamess();
        });

        function loadgamess() {
            fetch(API_URL)
                .then(response => response.json())
                .then(gamess => {
                    const gamesList = document.getElementById('gamesList');
                    gamesList.innerHTML = '';
                    gamess.forEach(games => {
                        gamesList.innerHTML += `
                            <tr>
                                <td>${games.id}</td>
                                <td>${games.title}</td>
                                <td>${games.genre}</td>
                                <td>${games.price}</td>
                                <td>${games.rating}</td>
                                <td class="btn-group-action">
                                    <button class="btn btn-sm btn-warning me-1" onclick="editgames(${games.id})">Edit</button>
                                    <button class="btn btn-sm btn-danger" onclick="deletegames(${games.id})">Hapus</button>
                                </td>
                            </tr>
                        `;
                    });
                })
                .catch(error => alert('Error loading gamess: ' + error));
        }

        function searchgames() {
            const id = document.getElementById('searchInput').value;
            if (!id) {
                loadgamess();
                return;
            }
            
            fetch(`${API_URL}/${id}`)
                .then(response => response.json())
                .then(games => {
                    const gamesList = document.getElementById('gamesList');
                    if (games.message) {
                        alert('games not found');
                        return;
                    }
                    gamesList.innerHTML = `
                        <tr>
                            <td>${games.id}</td>
                            <td>${games.title}</td>
                            <td>${games.genre}</td>
                            <td>${games.price}</td>
                            <td>${games.rating}</td>
                            <td class="btn-group-action">
                                <button class="btn btn-sm btn-warning me-1" onclick="editgames(${games.id})">Edit</button>
                                <button class="btn btn-sm btn-danger" onclick="deletegames(${games.id})">Hapus</button>
                            </td>
                        </tr>
                    `;
                })
                .catch(error => alert('Error searching games: ' + error));
        }

        function editgames(id) {
            fetch(`${API_URL}/${id}`)
                .then(response => response.json())
                .then(games => {
                    document.getElementById('gamesId').value = games.id;
                    document.getElementById('title').value = games.title;
                    document.getElementById('genre').value = games.genre;
                    document.getElementById('price').value = games.price;
                    document.getElementById('rating').value = games.rating;
                    document.getElementById('modaltitle').textContent = 'Edit Buku';
                    gamesModal.show();
                })
                .catch(error => alert('Error loading games details: ' + error));
        }

        function deletegames(id) {
            if (confirm('Are you sure you want to delete this games?')) {
                fetch(`${API_URL}/${id}`, {
                    method: 'DELETE'
                })
                .then(response => response.json())
                .then(data => {
                    alert('games deleted successfully');
                    loadgamess();
                })
                .catch(error => alert('Error deleting games: ' + error));
            }
        }

        function savegames() {
            const gamesId = document.getElementById('gamesId').value;
            const gamesData = {
                title: document.getElementById('title').value,
                genre: document.getElementById('genre').value,
                price: document.getElementById('price').value,
                rating: document.getElementById('rating').value
            };

            const method = gamesId ? 'PUT' : 'POST';
            const url = gamesId ? `${API_URL}/${gamesId}` : API_URL;

            fetch(url, {
                method: method,
                headers: {
                    'Content-genre': 'application/json'
                },
                body: JSON.stringify(gamesData)
            })
            .then(response => response.json())
            .then(data => {
                alert(gamesId ? 'games updated successfully' : 'games added successfully');
                gamesModal.hide();
                loadgamess();
                resetForm();
            })
            .catch(error => alert('Error saving games: ' + error));
        }

        function resetForm() {
            document.getElementById('gamesId').value = '';
            document.getElementById('gamesForm').reset();
            document.getElementById('modaltitle').textContent = 'Tambah Buku';
        }

        // Reset form when modal is closed
        document.getElementById('gamesModal').addEventListener('hidden.bs.modal', resetForm);
    </script>
    
    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
