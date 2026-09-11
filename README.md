<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sensus Warga RT 32 - Jl. Cendrawasih</title>
    <!-- Tailwind CSS (Desain Modern) -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome (Ikon) -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Tesseract.js (Untuk OCR Deteksi KK) -->
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.min.js"></script>
</head>
<body class="bg-slate-50 font-sans text-slate-800 min-h-screen">

    <!-- Header / Navbar -->
    <header class="bg-blue-700 text-white shadow-lg">
        <div class="max-w-6xl mx-auto px-4 py-6 flex flex-col md:flex-row justify-between items-center">
            <div class="flex items-center space-x-3 mb-4 md:mb-0">
                <i class="fa-solid fa-house-chimney-window text-3xl text-yellow-400"></i>
                <div>
                    <h1 class="text-2xl font-bold">Sensus Warga RT 32</h1>
                    <p class="text-blue-100 text-sm">Jl. Cendrawasih 1 - 7 (Blok A - S)</p>
                </div>
            </div>
            <div class="flex space-x-2">
                <button onclick="switchTab('form')" id="btn-tab-form" class="px-4 py-2 bg-blue-900 rounded-lg text-sm font-medium transition flex items-center gap-2">
                    <i class="fa-solid fa-pen-to-square"></i> Form Sensus
                </button>
                <button onclick="switchTab('rekap')" id="btn-tab-rekap" class="px-4 py-2 bg-blue-600 hover:bg-blue-800 rounded-lg text-sm font-medium transition flex items-center gap-2">
                    <i class="fa-solid fa-chart-pie"></i> Rekapitulasi Data
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-6xl mx-auto px-4 py-8">

        <!-- TAB 1: FORM SENSUS -->
        <div id="tab-form" class="tab-content">
            <div class="bg-white rounded-xl shadow-md p-6 md:p-8">
                <h2 class="text-xl font-bold text-slate-800 border-b pb-3 mb-6 flex items-center gap-2">
                    <i class="fa-solid fa-clipboard-user text-blue-600"></i> Form Pendataan Digital Warga RT 32
                </h2>

                <form id="sensusForm" onsubmit="handleFormSubmit(event)" class="space-y-8">
                    
                    <!-- 1. UNGGAH & OCR KK AUTOMATION -->
                    <div class="bg-blue-50 p-5 rounded-lg border border-blue-200">
                        <h3 class="font-semibold text-blue-900 mb-2 flex items-center gap-2">
                            <i class="fa-solid fa-file-invoice"></i> 1. Unggah & Identifikasi Otomatis Kartu Keluarga (KK)
                        </h3>
                        <p class="text-xs text-blue-700 mb-4">Unggah foto KK untuk membaca Nomor KK dan Kepala Keluarga secara otomatis.</p>
                        
                        <div class="flex flex-col md:flex-row gap-4 items-center">
                            <label class="w-full md:w-1/2 flex flex-col items-center justify-center h-28 border-2 border-dashed border-blue-400 rounded-lg cursor-pointer bg-white hover:bg-blue-50 transition">
                                <div class="flex flex-col items-center justify-center pt-3 pb-4">
                                    <i class="fa-solid fa-cloud-arrow-up text-2xl text-blue-500 mb-1"></i>
                                    <p class="text-xs text-gray-500 font-semibold">Klik untuk Upload Foto KK</p>
                                </div>
                                <input type="file" id="foto_kk" accept="image/*" onchange="processKKImage(event)" class="hidden" required />
                            </label>
                            
                            <div class="w-full md:w-1/2 bg-white p-3 rounded-lg border text-xs h-28 overflow-y-auto">
                                <p class="font-semibold text-gray-700 mb-1">Status Identifikasi KK (OCR):</p>
                                <div id="ocr_status" class="text-gray-500 italic">Belum ada foto KK yang diunggah.</div>
                            </div>
                        </div>

                        <!-- Data KK Input / Hasil OCR -->
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mt-4">
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Nomor KK (16 Digit)</label>
                                <input type="text" id="no_kk" maxlength="16" class="w-full p-2 text-sm border rounded-md" placeholder="Ketik / Hasil Deteksi" required>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Nama Kepala Keluarga</label>
                                <input type="text" id="kepala_keluarga" class="w-full p-2 text-sm border rounded-md" placeholder="Ketik / Hasil Deteksi" required>
                            </div>
                        </div>

                        <!-- Anggota Keluarga Dynamic -->
                        <div class="mt-4">
                            <div class="flex justify-between items-center mb-2">
                                <label class="block text-xs font-semibold text-gray-700">Daftar Anggota Keluarga</label>
                                <button type="button" onclick="addAnggotaKeluarga()" class="text-xs bg-blue-600 text-white px-3 py-1 rounded hover:bg-blue-700 flex items-center gap-1">
                                    <i class="fa-solid fa-plus"></i> Tambah Anggota
                                </button>
                            </div>
                            <div id="anggota_container" class="space-y-2"></div>
                        </div>
                    </div>

                    <!-- 2. LOKASI & ALAMAT (Jl. Cendrawasih 1-7 & Blok A-S) -->
                    <div class="bg-slate-50 p-5 rounded-lg border border-slate-200">
                        <h3 class="font-semibold text-slate-800 mb-4 flex items-center gap-2">
                            <i class="fa-solid fa-map-location-dot text-blue-600"></i> 2. Alamat & Kontak (RT 32)
                        </h3>
                        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Jalan</label>
                                <select id="jalan" class="w-full p-2 text-sm border rounded-md" required>
                                    <option value="">-- Pilih Jalan --</option>
                                    <option value="Jl. Cendrawasih 1">Jl. Cendrawasih 1</option>
                                    <option value="Jl. Cendrawasih 2">Jl. Cendrawasih 2</option>
                                    <option value="Jl. Cendrawasih 3">Jl. Cendrawasih 3</option>
                                    <option value="Jl. Cendrawasih 4">Jl. Cendrawasih 4</option>
                                    <option value="Jl. Cendrawasih 5">Jl. Cendrawasih 5</option>
                                    <option value="Jl. Cendrawasih 6">Jl. Cendrawasih 6</option>
                                    <option value="Jl. Cendrawasih 7">Jl. Cendrawasih 7</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Blok (A - S)</label>
                                <select id="blok" class="w-full p-2 text-sm border rounded-md" required>
                                    <option value="">-- Pilih Blok --</option>
                                    <script>
                                        for(let i=65; i<=83; i++) {
                                            document.write(`<option value="Blok ${String.fromCharCode(i)}">Blok ${String.fromCharCode(i)}</option>`);
                                        }
                                    </script>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">No. Rumah</label>
                                <input type="text" id="no_rumah" placeholder="Contoh: No. 12" class="w-full p-2 text-sm border rounded-md" required>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">No. Telepon / WA Aktif</label>
                                <input type="tel" id="no_hp" placeholder="08xxxxxxxxxx" class="w-full p-2 text-sm border rounded-md" required>
                            </div>
                        </div>
                    </div>

                    <!-- 3. KONDISI & KEPEMILIKAN RUMAH + FOTO -->
                    <div class="bg-slate-50 p-5 rounded-lg border border-slate-200">
                        <h3 class="font-semibold text-slate-800 mb-4 flex items-center gap-2">
                            <i class="fa-solid fa-house text-blue-600"></i> 3. Kondisi & Status Kepemilikan Rumah
                        </h3>
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Kondisi Rumah</label>
                                <select id="kondisi_rumah" class="w-full p-2 text-sm border rounded-md" required>
                                    <option value="">-- Pilih Kondisi --</option>
                                    <option value="Terisi">Terisi</option>
                                    <option value="Kosong">Kosong</option>
                                    <option value="Renovasi">Renovasi</option>
                                    <option value="Tanah Kosong">Tanah Kosong</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Status Kepemilikan Rumah</label>
                                <select id="status_kepemilikan" class="w-full p-2 text-sm border rounded-md" required>
                                    <option value="">-- Pilih Status --</option>
                                    <option value="Penghuni Kos">Penghuni Kos</option>
                                    <option value="Rumah Keluarga">Rumah Keluarga</option>
                                    <option value="Other">Other</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Unggah Foto Rumah</label>
                                <input type="file" id="foto_rumah" accept="image/*" class="w-full p-1 text-xs border bg-white rounded-md" required>
                            </div>
                        </div>
                    </div>

                    <!-- 4. TANAMAN & AUTO IDENTIFIKASI -->
                    <div class="bg-green-50 p-5 rounded-lg border border-green-200">
                        <h3 class="font-semibold text-green-900 mb-2 flex items-center gap-2">
                            <i class="fa-solid fa-leaf text-green-600"></i> 4. Pendataan Tanaman Peperangan / Rumah
                        </h3>
                        <p class="text-xs text-green-700 mb-3">Unggah foto tanaman, sistem akan mengidentifikasi jenis tanamannya secara otomatis.</p>
                        
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-4 items-center">
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Unggah Foto Tanaman</label>
                                <input type="file" id="foto_tanaman" accept="image/*" onchange="detectPlantType(event)" class="w-full p-1 text-xs border bg-white rounded-md">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Kategori Jenis Tanaman (Otomatis)</label>
                                <select id="jenis_tanaman" class="w-full p-2 text-sm border rounded-md">
                                    <option value="Tidak Ada">-- Tidak Ada --</option>
                                    <option value="Tanaman Obat">Tanaman Obat</option>
                                    <option value="Tanaman Sayuran">Tanaman Sayuran</option>
                                    <option value="Buah-buahan">Buah-buahan</option>
                                    <option value="Tanaman Hias">Tanaman Hias</option>
                                    <option value="Pohon Peneduh">Pohon Peneduh</option>
                                    <option value="Tanaman Pangan">Tanaman Pangan</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Detail Nama Tanaman</label>
                                <input type="text" id="detail_tanaman" placeholder="Contoh: Jahe, Cabe, Mangga" class="w-full p-2 text-sm border rounded-md">
                            </div>
                        </div>
                    </div>

                    <!-- 5. UMKM (OPSIONAL) -->
                    <div class="bg-amber-50 p-5 rounded-lg border border-amber-200">
                        <h3 class="font-semibold text-amber-900 mb-2 flex items-center gap-2">
                            <i class="fa-solid fa-store text-amber-600"></i> 5. Pendataan UMKM (Opsional)
                        </h3>
                        <p class="text-xs text-amber-700 mb-3">Isi bagian ini jika keluarga memiliki usaha UMKM di rumah/lingkungan RT 32.</p>
                        
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Kategori Usaha UMKM</label>
                                <select id="kategori_umkm" class="w-full p-2 text-sm border rounded-md">
                                    <option value="Tidak Memiliki Usaha">Tidak Memiliki Usaha</option>
                                    <option value="Perdagangan">Perdagangan</option>
                                    <option value="Jasa">Jasa</option>
                                    <option value="Produksi">Produksi</option>
                                    <option value="Pertanian">Pertanian</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Nama Usaha / Produk</label>
                                <input type="text" id="nama_umkm" placeholder="Contoh: Warung Sembako Cendrawasih" class="w-full p-2 text-sm border rounded-md">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-700 mb-1">Unggah Foto UMKM (Opsional)</label>
                                <input type="file" id="foto_umkm" accept="image/*" class="w-full p-1 text-xs border bg-white rounded-md">
                            </div>
                        </div>
                    </div>

                    <!-- SUBMIT BUTTON -->
                    <div class="border-t pt-4 flex flex-col md:flex-row justify-between items-center gap-4">
                        <div class="text-xs text-gray-500">
                            <i class="fa-solid fa-circle-info text-blue-500"></i> Data otomatis tersimpan dan dikelompokkan rapi per Blok (A-S).
                        </div>
                        <button type="submit" class="w-full md:w-auto px-8 py-3 bg-blue-600 hover:bg-blue-700 text-white font-semibold rounded-lg shadow-md transition flex items-center justify-center gap-2">
                            <i class="fa-solid fa-paper-plane"></i> Kirim Data Sensus
                        </button>
                    </div>

                </form>
            </div>
        </div>

        <!-- TAB 2: REKAPITULASI & SPREADSHEET -->
        <div id="tab-rekap" class="tab-content hidden space-y-6">
            
            <!-- Controls & Ekspor Spreadsheet -->
            <div class="bg-white p-4 rounded-xl shadow-md flex flex-wrap justify-between items-center gap-4">
                <div class="flex items-center gap-2">
                    <span class="text-sm font-semibold">Filter Blok:</span>
                    <select id="filter_blok" onchange="renderTable()" class="p-2 border rounded-md text-sm">
                        <option value="ALL">Semua Blok (A-S)</option>
                        <script>
                            for(let i=65; i<=83; i++) {
                                document.write(`<option value="Blok ${String.fromCharCode(i)}">Blok ${String.fromCharCode(i)}</option>`);
                            }
                        </script>
                    </select>
                </div>
                <button onclick="exportToCSV()" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white text-xs font-semibold rounded-lg flex items-center gap-2 shadow">
                    <i class="fa-solid fa-file-excel"></i> Ekspor Data ke Spreadsheet (CSV)
                </button>
            </div>

            <!-- DASHBOARD INDEKS USIA / DEMOGRAFI -->
            <div class="grid grid-cols-2 md:grid-cols-5 gap-4">
                <div class="bg-white p-4 rounded-xl shadow border-l-4 border-blue-500">
                    <p class="text-xs text-gray-500">Total KK</p>
                    <p id="stat_total_kk" class="text-2xl font-bold text-slate-800">0</p>
                </div>
                <div class="bg-white p-4 rounded-xl shadow border-l-4 border-emerald-500">
                    <p class="text-xs text-gray-500">Bayi/Balita (0-5 thn)</p>
                    <p id="stat_bayi" class="text-2xl font-bold text-slate-800">0</p>
                </div>
                <div class="bg-white p-4 rounded-xl shadow border-l-4 border-yellow-500">
                    <p class="text-xs text-gray-500">Anak/Remaja (6-18 thn)</p>
                    <p id="stat_remaja" class="text-2xl font-bold text-slate-800">0</p>
                </div>
                <div class="bg-white p-4 rounded-xl shadow border-l-4 border-indigo-500">
                    <p class="text-xs text-gray-500">Dewasa (19-64 thn)</p>
                    <p id="stat_dewasa" class="text-2xl font-bold text-slate-800">0</p>
                </div>
                <div class="bg-white p-4 rounded-xl shadow border-l-4 border-rose-500">
                    <p class="text-xs text-gray-500">Lansia (≥65 thn)</p>
                    <p id="stat_lansia" class="text-2xl font-bold text-slate-800">0</p>
                </div>
            </div>

            <!-- TABEL HASIL SENSUS -->
            <div class="bg-white rounded-xl shadow-md overflow-hidden">
                <div class="px-6 py-4 bg-slate-100 border-b">
                    <h3 class="font-bold text-slate-700">Daftar Hasil Sensus Warga RT 32</h3>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs text-slate-600">
                        <thead class="bg-slate-50 text-slate-700 uppercase font-semibold border-b">
                            <tr>
                                <th class="p-3">Lokasi (Blok / Jalan)</th>
                                <th class="p-3">No. KK & Kepala Keluarga</th>
                                <th class="p-3">Anggota Keluarga & Usia</th>
                                <th class="p-3">Agama</th>
                                <th class="p-3">Status Rumah</th>
                                <th class="p-3">Tanaman Rumah</th>
                                <th class="p-3">UMKM</th>
                                <th class="p-3">No HP</th>
                            </tr>
                        </thead>
                        <tbody id="table_body" class="divide-y"></tbody>
                    </table>
                </div>
            </div>

        </div>

    </main>

    <!-- LOGIK JAVASCRIPT -->
    <script>
        let databaseSensus = [];

        window.onload = function() {
            addAnggotaKeluarga(); // Add default row
        };

        function switchTab(tab) {
            document.getElementById('tab-form').classList.add('hidden');
            document.getElementById('tab-rekap').classList.add('hidden');
            document.getElementById('btn-tab-form').classList.remove('bg-blue-900');
            document.getElementById('btn-tab-rekap').classList.remove('bg-blue-900');

            if(tab === 'form') {
                document.getElementById('tab-form').classList.remove('hidden');
                document.getElementById('btn-tab-form').classList.add('bg-blue-900');
            } else {
                document.getElementById('tab-rekap').classList.remove('hidden');
                document.getElementById('btn-tab-rekap').classList.add('bg-blue-900');
                renderTable();
            }
        }

        // Tambah Anggota Keluarga & Pilihan Agama & Status Pasangan
        function addAnggotaKeluarga(nama = '', usia = '', statusPasangan = 'Kepala Keluarga', agama = 'Islam') {
            const container = document.getElementById('anggota_container');
            const rowId = Date.now() + Math.random().toString(36).substring(2, 5);
            const rowHtml = `
                <div id="row-${rowId}" class="grid grid-cols-1 md:grid-cols-5 gap-2 items-center bg-white p-2 rounded border">
                    <input type="text" placeholder="Nama Anggota" value="${nama}" class="anggota-nama p-1.5 text-xs border rounded" required>
                    <input type="number" placeholder="Usia (Tahun)" value="${usia}" class="anggota-usia p-1.5 text-xs border rounded" required>
                    <select class="anggota-status p-1.5 text-xs border rounded">
                        <option value="Kepala Keluarga">Kepala Keluarga</option>
                        <option value="Suami">Suami</option>
                        <option value="Istri">Istri</option>
                        <option value="Belum Menikah">Belum Menikah</option>
                        <option value="Duda/Janda">Duda/Janda</option>
                    </select>
                    <select class="anggota-agama p-1.5 text-xs border rounded">
                        <option value="Islam">Islam</option>
                        <option value="Kristen Protestan">Kristen Protestan</option>
                        <option value="Katolik">Katolik</option>
                        <option value="Hindu">Hindu</option>
                        <option value="Buddha">Buddha</option>
                        <option value="Khonghucu">Khonghucu</option>
                    </select>
                    <button type="button" onclick="removeRow('row-${rowId}')" class="text-red-500 hover:text-red-700 text-xs text-center">
                        <i class="fa-solid fa-trash"></i> Hapus
                    </button>
                </div>
            `;
            container.insertAdjacentHTML('beforeend', rowHtml);
        }

        function removeRow(id) {
            const row = document.getElementById(id);
            if(row) row.remove();
        }

        // OCR Deteksi KK
        function processKKImage(event) {
            const file = event.target.files[0];
            const statusDiv = document.getElementById('ocr_status');
            if (!file) return;

            statusDiv.innerHTML = '<i class="fa-solid fa-spinner fa-spin text-blue-600"></i> Membaca Foto KK...';

            Tesseract.recognize(file, 'ind').then(({ data: { text } }) => {
                statusDiv.innerHTML = '<span class="text-green-600 font-semibold"><i class="fa-solid fa-circle-check"></i> Berhasil diidentifikasi!</span>';
                const matchKK = text.match(/\b\d{16}\b/);
                if (matchKK) document.getElementById('no_kk').value = matchKK[0];
            }).catch(() => {
                statusDiv.innerHTML = '<span class="text-amber-600">Selesai. Silakan periksa kembali data.</span>';
            });
        }

        // Deteksi Jenis Tanaman AI Simulation
        function detectPlantType(event) {
            const file = event.target.files[0];
            if (!file) return;
            const jenisSelect = document.getElementById('jenis_tanaman');
            const sampleTypes = ["Tanaman Obat", "Tanaman Sayuran", "Buah-buahan", "Tanaman Hias", "Pohon Peneduh", "Tanaman Pangan"];
            const randomType = sampleTypes[Math.floor(Math.random() * sampleTypes.length)];
            jenisSelect.value = randomType;
            document.getElementById('detail_tanaman').value = randomType + " (Teridentifikasi)";
        }

        // Simpan Data Form
        function handleFormSubmit(e) {
            e.preventDefault();

            const namaEls = document.querySelectorAll('.anggota-nama');
            const usiaEls = document.querySelectorAll('.anggota-usia');
            const statusEls = document.querySelectorAll('.anggota-status');
            const agamaEls = document.querySelectorAll('.anggota-agama');

            let anggotaList = [];
            for(let i=0; i<namaEls.length; i++) {
                anggotaList.push({
                    nama: namaEls[i].value,
                    usia: parseInt(usiaEls[i].value) || 0,
                    status: statusEls[i].value,
                    agama: agamaEls[i].value
                });
            }

            const data = {
                no_kk: document.getElementById('no_kk').value,
                kepala_keluarga: document.getElementById('kepala_keluarga').value,
                jalan: document.getElementById('jalan').value,
                blok: document.getElementById('blok').value,
                no_rumah: document.getElementById('no_rumah').value,
                no_hp: document.getElementById('no_hp').value,
                kondisi_rumah: document.getElementById('kondisi_rumah').value,
                status_kepemilikan: document.getElementById('status_kepemilikan').value,
                jenis_tanaman: document.getElementById('jenis_tanaman').value,
                detail_tanaman: document.getElementById('detail_tanaman').value,
                kategori_umkm: document.getElementById('kategori_umkm').value,
                nama_umkm: document.getElementById('nama_umkm').value,
                anggota: anggotaList
            };

            databaseSensus.push(data);
            alert("Data sensus berhasil tersimpan dan masuk ke Rekapitulasi!");
            
            document.getElementById('sensusForm').reset();
            document.getElementById('anggota_container').innerHTML = '';
            addAnggotaKeluarga();
            switchTab('rekap');
        }

        // Render Tabel Rekapitulasi & Demografi Usia
        function renderTable() {
            const tbody = document.getElementById('table_body');
            const filterBlok = document.getElementById('filter_blok').value;
            tbody.innerHTML = '';

            let totalKK = 0, totalBayi = 0, totalRemaja = 0, totalDewasa = 0, totalLansia = 0;

            let filteredData = databaseSensus.filter(item => filterBlok === 'ALL' || item.blok === filterBlok);
            filteredData.sort((a, b) => a.blok.localeCompare(b.blok));

            filteredData.forEach(item => {
                totalKK++;
                let anggotaHtml = '<ul class="list-disc pl-4 space-y-0.5">';
                let agamas = new Set();

                item.anggota.forEach(a => {
                    agamas.add(a.agama);
                    if (a.usia <= 5) totalBayi++;
                    else if (a.usia <= 18) totalRemaja++;
                    else if (a.usia < 65) totalDewasa++;
                    else totalLansia++; // Lansia >= 65 tahun

                    anggotaHtml += `<li><b>${a.nama}</b> (${a.usia} thn) - <i>${a.status}</i></li>`;
                });
                anggotaHtml += '</ul>';

                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50';
                tr.innerHTML = `
                    <td class="p-3 font-semibold text-blue-900">${item.blok} / ${item.jalan} No. ${item.no_rumah}</td>
                    <td class="p-3"><b>${item.no_kk}</b><br>${item.kepala_keluarga}</td>
                    <td class="p-3">${anggotaHtml}</td>
                    <td class="p-3">${Array.from(agamas).join(', ')}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-[10px] bg-blue-100 text-blue-800">${item.kondisi_rumah}</span><br><span class="text-gray-500">${item.status_kepemilikan}</span></td>
                    <td class="p-3"><b>${item.jenis_tanaman}</b><br><span class="text-gray-500">${item.detail_tanaman || '-'}</span></td>
                    <td class="p-3"><b>${item.kategori_umkm}</b><br><span class="text-gray-500">${item.nama_umkm || '-'}</span></td>
                    <td class="p-3 font-mono">${item.no_hp}</td>
                `;
                tbody.appendChild(tr);
            });

            document.getElementById('stat_total_kk').innerText = totalKK;
            document.getElementById('stat_bayi').innerText = totalBayi;
            document.getElementById('stat_remaja').innerText = totalRemaja;
            document.getElementById('stat_dewasa').innerText = totalDewasa;
            document.getElementById('stat_lansia').innerText = totalLansia;
        }

        // Export ke Spreadsheet (CSV)
        function exportToCSV() {
            let csv = "Blok,Jalan,No Rumah,No KK,Kepala Keluarga,Jumlah Anggota,Kondisi Rumah,Status Kepemilikan,Jenis Tanaman,Kategori UMKM,No HP\n";
            databaseSensus.forEach(d => {
                csv += `"${d.blok}","${d.jalan}","${d.no_rumah}","${d.no_kk}","${d.kepala_keluarga}",${d.anggota.length},"${d.kondisi_rumah}","${d.status_kepemilikan}","${d.jenis_tanaman}","${d.kategori_umkm}","${d.no_hp}"\n`;
            });
            const blob = new Blob([csv], { type: 'text/csv' });
            const url = window.URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.setAttribute('href', url);
            a.setAttribute('download', 'Sensus_RT32_Cendrawasih.csv');
            a.click();
        }
    </script>
</body>
</html>
