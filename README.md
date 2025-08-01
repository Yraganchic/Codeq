<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text Files Manager</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .file-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        }
        .modal {
            transition: all 0.3s ease;
        }
        .preview-image {
            max-height: 200px;
            object-fit: contain;
        }
        .fa-search {
            width: 1rem;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <!-- Header -->
        <header class="mb-8">
            <h1 class="text-4xl font-bold text-center text-indigo-700 mb-2">Text Files Manager</h1>
            <p class="text-center text-gray-600">Создавайте, редактируйте и храните свои текстовые файлы</p>
        </header>

      
        <div class="flex justify-center mb-8">
            <button id="addFileBtn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-6 rounded-full flex items-center transition-all">
                <i class="fas fa-plus mr-2"></i> Добавить файл
            </button>
        </div>

      
        <div class="mb-8 bg-white p-4 rounded-lg shadow">
            <div class="flex flex-col md:flex-row gap-4">
                <div class="relative flex-grow">
                    <input type="text" id="searchInput" placeholder="Поиск по названию или описанию..." class="w-full p-3 pl-10 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    <button id="searchButton" class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600">
                        <i class="fas fa-search"></i>
                    </button>
                </div>
                <select id="filterSelect" class="p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    <option value="all">Все файлы</option>
                    <option value="text">Только текст</option>
                    <option value="file">Только файлы</option>
                </select>
            </div>
        </div>

        <!-- Files Grid -->
        <div id="filesContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <!-- Files will be displayed here -->
        </div>

        <!-- No Files Message -->
        <div id="noFilesMessage" class="text-center py-12 hidden">
            <i class="fas fa-folder-open text-5xl text-gray-400 mb-4"></i>
            <h3 class="text-2xl font-semibold text-gray-600">Файлов пока нет</h3>
            <p class="text-gray-500">Добавьте свой первый файл, нажав на кнопку выше</p>
        </div>
    </div>

    <!-- Add/Edit File Modal -->
    <div id="fileModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden modal">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-2xl max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <div class="flex justify-between items-center mb-4">
                    <h3 id="modalTitle" class="text-2xl font-bold text-gray-800">Добавить новый файл</h3>
                    <button id="closeModalBtn" class="text-gray-500 hover:text-gray-700">
                        <i class="fas fa-times text-2xl"></i>
                    </button>
                </div>

                <form id="fileForm" class="space-y-4">
                    <input type="hidden" id="fileId">

                    <div>
                        <label for="nickname" class="block text-sm font-medium text-gray-700 mb-1">Ваш никнейм</label>
                        <input type="text" id="nickname" required class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>

                    <div>
                        <label for="title" class="block text-sm font-medium text-gray-700 mb-1">Название файла</label>
                        <input type="text" id="title" required class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500">
                    </div>

                    <div>
                        <label for="description" class="block text-sm font-medium text-gray-700 mb-1">Описание</label>
                        <textarea id="description" rows="2" class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"></textarea>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">Тип контента</label>
                            <div class="flex space-x-4">
                                <label class="inline-flex items-center">
                                    <input type="radio" name="contentType" value="text" checked class="h-4 w-4 text-indigo-600 focus:ring-indigo-500">
                                    <span class="ml-2 text-gray-700">Текст</span>
                                </label>
                                <label class="inline-flex items-center">
                                    <input type="radio" name="contentType" value="file" class="h-4 w-4 text-indigo-600 focus:ring-indigo-500">
                                    <span class="ml-2 text-gray-700">Файл</span>
                                </label>
                            </div>
                        </div>

                        <div id="imageUploadContainer" class="hidden">
                            <label for="imageUpload" class="block text-sm font-medium text-gray-700 mb-1">Изображение (опционально)</label>
                            <input type="file" id="imageUpload" accept="image/*" class="w-full">
                        </div>
                    </div>

                    <div id="textContentContainer">
                        <label for="content" class="block text-sm font-medium text-gray-700 mb-1">Текст</label>
                        <textarea id="content" rows="6" class="w-full p-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"></textarea>
                    </div>

                    <div id="fileContentContainer" class="hidden">
                        <label for="fileUpload" class="block text-sm font-medium text-gray-700 mb-1">Загрузить текстовый файл</label>
                        <input type="file" id="fileUpload" accept=".txt,.doc,.docx,.pdf" class="w-full p-2 border border-gray-300 rounded-lg">
                        <div id="filePreview" class="mt-4 hidden">
                            <h4 class="font-medium text-gray-700 mb-2">Предпросмотр файла:</h4>
                            <div class="bg-gray-100 p-4 rounded-lg max-h-40 overflow-y-auto">
                                <pre id="fileContentPreview" class="whitespace-pre-wrap text-sm"></pre>
                            </div>
                        </div>
                    </div>

                    <div id="imagePreviewContainer" class="hidden mt-4">
                        <h4 class="font-medium text-gray-700 mb-2">Предпросмотр изображения:</h4>
                        <img id="imagePreview" src="#" alt="Предпросмотр" class="preview-image w-full object-cover rounded-lg border border-gray-200">
                    </div>

                    <div class="flex justify-end space-x-3 pt-4">
                        <button type="button" id="cancelBtn" class="px-4 py-2 border border-gray-300 rounded-lg text-gray-700 hover:bg-gray-100">Отмена</button>
                        <button type="submit" id="saveBtn" class="px-4 py-2 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700">Сохранить</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <!-- File Preview Modal -->
    <div id="previewModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50 hidden modal">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-3xl max-h-[90vh] overflow-y-auto">
            <div class="p-6">
                <div class="flex justify-between items-center mb-4">
                    <h3 id="previewTitle" class="text-2xl font-bold text-gray-800"></h3>
                    <button id="closePreviewBtn" class="text-gray-500 hover:text-gray-700">
                        <i class="fas fa-times text-2xl"></i>
                    </button>
                </div>

                <div class="flex items-center mb-4">
                    <div class="w-10 h-10 rounded-full bg-indigo-100 flex items-center justify-center text-indigo-600 font-semibold mr-3" id="previewNicknameIcon"></div>
                    <div>
                        <p id="previewNickname" class="font-medium text-gray-700"></p>
                        <p id="previewDate" class="text-sm text-gray-500"></p>
                    </div>
                </div>

                <p id="previewDescription" class="text-gray-700 mb-6"></p>

                <div id="previewImageContainer" class="mb-6 hidden">
                    <img id="previewImage" src="#" alt="Изображение" class="preview-image w-full rounded-lg border border-gray-200">
                </div>

                <div class="bg-gray-50 p-4 rounded-lg">
                    <pre id="previewContent" class="whitespace-pre-wrap text-gray-800"></pre>
                </div>

                <div class="flex justify-end space-x-3 pt-6">
                    <button id="editFileBtn" class="px-4 py-2 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700">Редактировать</button>
                    <button id="deleteFileBtn" class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700">Удалить</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // DOM Elements
            const addFileBtn = document.getElementById('addFileBtn');
            const fileModal = document.getElementById('fileModal');
            const closeModalBtn = document.getElementById('closeModalBtn');
            const cancelBtn = document.getElementById('cancelBtn');
            const fileForm = document.getElementById('fileForm');
            const filesContainer = document.getElementById('filesContainer');
            const noFilesMessage = document.getElementById('noFilesMessage');
            const searchInput = document.getElementById('searchInput');
            const filterSelect = document.getElementById('filterSelect');
            const previewModal = document.getElementById('previewModal');
            const closePreviewBtn = document.getElementById('closePreviewBtn');
            const editFileBtn = document.getElementById('editFileBtn');
            const deleteFileBtn = document.getElementById('deleteFileBtn');
            
            // Content type radios
            const contentTypeRadios = document.querySelectorAll('input[name="contentType"]');
            const textContentContainer = document.getElementById('textContentContainer');
            const fileContentContainer = document.getElementById('fileContentContainer');
            const imageUploadContainer = document.getElementById('imageUploadContainer');
            
            // File upload elements
            const fileUpload = document.getElementById('fileUpload');
            const filePreview = document.getElementById('filePreview');
            const fileContentPreview = document.getElementById('fileContentPreview');
            const imageUpload = document.getElementById('imageUpload');
            const imagePreviewContainer = document.getElementById('imagePreviewContainer');
            const imagePreview = document.getElementById('imagePreview');
            
            // State
            let files = JSON.parse(localStorage.getItem('textFiles')) || [];
            let currentFileId = null;
            
            // Initialize
            renderFiles();
            checkEmptyState();
            
            // Event Listeners
            addFileBtn.addEventListener('click', openAddFileModal);
            closeModalBtn.addEventListener('click', closeFileModal);
            cancelBtn.addEventListener('click', closeFileModal);
            fileForm.addEventListener('submit', handleFileSubmit);
            searchInput.addEventListener('input', handleSearch);
            searchInput.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    handleSearch();
                }
            });
            document.getElementById('searchButton').addEventListener('click', function(e) {
                e.preventDefault();
                handleSearch();
            });
            filterSelect.addEventListener('change', handleFilter);
            closePreviewBtn.addEventListener('click', closePreviewModal);
            editFileBtn.addEventListener('click', editCurrentFile);
            deleteFileBtn.addEventListener('click', deleteCurrentFile);
            
            // Content type change
            contentTypeRadios.forEach(radio => {
                radio.addEventListener('change', function() {
                    if (this.value === 'text') {
                        textContentContainer.classList.remove('hidden');
                        fileContentContainer.classList.add('hidden');
                        imageUploadContainer.classList.remove('hidden');
                    } else {
                        textContentContainer.classList.add('hidden');
                        fileContentContainer.classList.remove('hidden');
                        imageUploadContainer.classList.add('hidden');
                    }
                });
            });
            
            // File upload preview
            fileUpload.addEventListener('change', function(e) {
                const file = e.target.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        fileContentPreview.textContent = e.target.result;
                        filePreview.classList.remove('hidden');
                    };
                    reader.readAsText(file);
                }
            });
            
            // Image upload preview
            imageUpload.addEventListener('change', function(e) {
                const file = e.target.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        imagePreview.src = e.target.result;
                        imagePreviewContainer.classList.remove('hidden');
                    };
                    reader.readAsDataURL(file);
                }
            });
            
            // Functions
            function openAddFileModal() {
                currentFileId = null;
                document.getElementById('modalTitle').textContent = 'Добавить новый файл';
                document.getElementById('fileForm').reset();
                filePreview.classList.add('hidden');
                imagePreviewContainer.classList.add('hidden');
                document.querySelector('input[name="contentType"][value="text"]').checked = true;
                textContentContainer.classList.remove('hidden');
                fileContentContainer.classList.add('hidden');
                imageUploadContainer.classList.remove('hidden');
                fileModal.classList.remove('hidden');
            }
            
            function closeFileModal() {
                fileModal.classList.add('hidden');
            }
            
            function closePreviewModal() {
                previewModal.classList.add('hidden');
            }
            
            function handleFileSubmit(e) {
                e.preventDefault();
                
                const nickname = document.getElementById('nickname').value;
                const title = document.getElementById('title').value;
                const description = document.getElementById('description').value;
                const contentType = document.querySelector('input[name="contentType"]:checked').value;
                
                let content = '';
                let imageData = '';
                
                if (contentType === 'text') {
                    content = document.getElementById('content').value;
                } else {
                    if (fileUpload.files.length > 0) {
                        const reader = new FileReader();
                        reader.onload = function(e) {
                            content = e.target.result;
                            saveFile(nickname, title, description, contentType, content, imageData);
                        };
                        reader.readAsText(fileUpload.files[0]);
                    } else {
                        alert('Пожалуйста, загрузите файл');
                        return;
                    }
                }
                
                if (imageUpload.files.length > 0) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        imageData = e.target.result;
                        saveFile(nickname, title, description, contentType, content, imageData);
                    };
                    reader.readAsDataURL(imageUpload.files[0]);
                } else {
                    saveFile(nickname, title, description, contentType, content, imageData);
                }
            }
            
            function saveFile(nickname, title, description, contentType, content, imageData) {
                const now = new Date();
                const dateString = now.toLocaleDateString('ru-RU', {
                    day: 'numeric',
                    month: 'long',
                    year: 'numeric',
                    hour: '2-digit',
                    minute: '2-digit'
                });
                
                if (currentFileId) {
                    // Update existing file
                    const index = files.findIndex(f => f.id === currentFileId);
                    if (index !== -1) {
                        files[index] = {
                            ...files[index],
                            nickname,
                            title,
                            description,
                            contentType,
                            content,
                            image: imageData || files[index].image,
                            updatedAt: dateString
                        };
                    }
                } else {
                    // Add new file
                    const newFile = {
                        id: Date.now().toString(),
                        nickname,
                        title,
                        description,
                        contentType,
                        content,
                        image: imageData,
                        createdAt: dateString,
                        updatedAt: dateString
                    };
                    files.unshift(newFile);
                }
                
                localStorage.setItem('textFiles', JSON.stringify(files));
                renderFiles();
                checkEmptyState();
                closeFileModal();
            }
            
            function renderFiles(filteredFiles = null) {
     
