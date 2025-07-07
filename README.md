<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive ML Learning Resources Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- 
        Chosen Palette: Warm Neutrals (Slate, Stone) with a Teal accent.
        The palette is designed to be calm, professional, and easy on the eyes for prolonged Browse. The warm neutral background (slate-50) and content areas (white) provide a clean canvas. Darker slate tones are used for text and navigation to ensure readability. The teal accent is used sparingly for interactive elements like buttons and active states to guide the user's attention without being distracting.
    -->
    <!-- 
        Application Structure Plan: 
        The application is designed as a single-page dashboard for optimal usability. The structure consists of a fixed sidebar for navigation and a main content area.
        - Sidebar Navigation: A list of the 9 ML model creation stages allows for quick, one-click access to any topic. This is more intuitive than scrolling through a long, linear document.
        - Main Content Area: This area is dynamic and presents the resources for the selected topic. It includes:
            1. An introductory paragraph for each section to provide context.
            2. Interactive filters (search bar, year toggles) at the top for easy discovery.
            3. A grid of resource cards, which is a visually organized and scannable way to present individual videos.
            4. An interactive summary chart at the top to give a quick overview of the content distribution.
        This structure was chosen because it separates navigation from content, allows for direct access to information, and provides powerful tools (filtering, search, visualization) to help users quickly find the most relevant resources, which is the primary goal of exploring a curated list.
    -->
    <!-- 
        Visualization & Content Choices:
        - Summary Bar Chart (Chart.js): 
            - Report Info: The number of video resources per section.
            - Goal: Provide a quick, high-level overview of the content distribution. Helps users see which topics have more or fewer resources.
            - Viz/Presentation: A horizontal bar chart is used for clear labeling of categories.
            - Interaction: Clicking on a bar in the chart will filter the view to show only the resources for that section, providing an intuitive, visual way to navigate.
            - Justification: This visual summary is more engaging than a simple list and adds an interactive discovery layer.
        - Resource Cards (HTML/CSS):
            - Report Info: Individual video details (title, creator, date, URL, focus).
            - Goal: Inform & Organize. Present each resource in a clear, structured, and visually appealing manner.
            - Viz/Presentation: Card layout with distinct fields for each piece of information. An external link icon (Unicode) clearly indicates an off-site link.
            - Interaction: The entire card or a specific "Watch" button is clickable, opening the video in a new tab. Hover effects provide visual feedback.
            - Justification: Cards are a standard and effective UI pattern for presenting list items with multiple attributes. They are responsive and easy to read.
        - Filters (HTML/JS):
            - Report Info: Publication year of videos.
            - Goal: Allow users to find the most up-to-date content easily.
            - Viz/Presentation: A series of toggle buttons for years.
            - Interaction: Clicking a year button filters the visible resource cards. An "All" button resets the filter.
            - Justification: Addresses the user's need for recent content ("updated videos") and adds significant interactive value.
    -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f8fafc; /* slate-50 */
        }
        .sidebar-item {
            transition: all 0.2s ease-in-out;
        }
        .resource-card {
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .resource-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
        }
        .filter-btn.active {
            background-color: #14b8a6; /* teal-500 */
            color: white;
            font-weight: 600;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 900px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 400px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 350px;
            }
        }
    </style>
</head>
<body class="text-slate-800">

    <div class="flex h-screen">
        <!-- Sidebar -->
        <aside id="sidebar" class="w-64 bg-white shadow-md flex-shrink-0 p-4 overflow-y-auto">
            <h1 class="text-2xl font-bold text-slate-900 mb-6">ML Learning Path</h1>
            <nav id="navigation" class="space-y-2">
                <!-- Navigation items will be injected here -->
            </nav>
        </aside>

        <!-- Main content -->
        <main class="flex-1 p-6 md:p-8 lg:p-10 overflow-y-auto">
            <header class="mb-8">
                <h2 id="section-title" class="text-3xl font-bold text-slate-900">Welcome!</h2>
                <p id="section-intro" class="mt-2 text-slate-600">Select a topic from the sidebar to explore curated video resources for each stage of the machine learning lifecycle.</p>
            </header>

            <!-- Filters -->
            <div id="filters" class="mb-8 p-4 bg-white rounded-lg shadow-sm hidden">
                <div class="flex flex-col md:flex-row gap-4 items-center">
                    <div class="relative w-full md:w-1/2">
                        <input type="text" id="search-input" placeholder="Search by keyword..." class="w-full pl-10 pr-4 py-2 border border-slate-300 rounded-full focus:outline-none focus:ring-2 focus:ring-teal-500">
                        <span class="absolute left-3 top-1/2 -translate-y-1/2 text-slate-400">&#128269;</span>
                    </div>
                    <div class="flex items-center gap-2">
                        <span class="text-sm font-medium text-slate-600">Year:</span>
                        <div id="year-filters" class="flex gap-2">
                           <!-- Year filter buttons will be injected here -->
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Chart Container -->
            <div id="chart-section" class="mb-8 p-4 bg-white rounded-lg shadow-sm">
                <div class="chart-container">
                    <canvas id="summaryChart"></canvas>
                </div>
            </div>

            <!-- Resources Grid -->
            <div id="resources-grid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6">
                <!-- Resource cards will be injected here -->
            </div>
            
             <div id="welcome-message" class="text-center p-10">
                <p class="text-lg text-slate-500">Your learning resources will appear here.</p>
            </div>
        </main>
    </div>

<script>
const resourcesData = [
    {
        section: "Project Setup & Data Loading",
        intro: "The foundation of any machine learning project. These videos cover setting up your development environment and loading initial datasets, ensuring you start with a solid and organized codebase.",
        videos: [
            { title: "The Ultimate VS Code Setup for Data & AI Projects", creator: "Dave Ebbelaar", date: "2024-08-01", url: "https://www.youtube.com/watch?v=mpk4Q5feWaw", focus: "A practical guide to setting up your development environment, which is crucial before diving into coding." },
            { title: "The Complete Machine Learning Roadmap", creator: "Programming with Mosh", date: "2024-07-18", url: "https://www.youtube.com/watch?v=7IgVGSaQPaw", focus: "Provides a high-level roadmap for machine learning, helping understand where data loading and project setup fit." },
            { title: "Pandas Full Python Course - Data Science Fundamentals", creator: "NeuralNine", date: "2025-01-24", url: "https://www.youtube.com/watch?v=EhYC02PD_gc", focus: "A comprehensive course on Pandas, essential for data loading and initial data handling in Python." }
        ]
    },
    {
        section: "Data Collection",
        intro: "Learn how to gather data for your projects. Web scraping is a powerful skill for collecting unique datasets from the internet when pre-packaged data isn't available.",
        videos: [
            { title: "Python Web Scraping is Easy", creator: "Absent Data", date: "2024-02-19", url: "https://www.youtube.com/watch?v=_mW20F5TuXU", focus: "A quick introduction to web scraping, demonstrating basic code for data extraction." },
            { title: "Ultimate Web Scraping tutorial", creator: "CodeWithHarry", date: "2024-11-08", url: "https://www.youtube.com/watch?v=1227R6KY8Ts", focus: "A more in-depth tutorial on web scraping, providing a comprehensive guide with code examples." },
            { title: "Web Scraping Tutorial Using Python | BeautifulSoup", creator: "CodeWithHarry", date: "2023-05-12", url: "https://www.youtube.com/watch?v=4tAp9Lu0eDI", focus: "A detailed tutorial specifically on using BeautifulSoup for web scraping." }
        ]
    },
    {
        section: "Data Preprocessing & Cleaning",
        intro: "Raw data is often messy. This crucial stage involves cleaning, handling missing values, and transforming data into a usable format for your models. Quality input leads to quality output.",
        videos: [
            { title: "Pandas Tutorial Data Cleaning Full Course 2025", creator: "Kadnob", date: "2025-06-05", url: "https://www.youtube.com/watch?v=WDOBsZK9Sc4", focus: "A very recent course specifically on data cleaning using Pandas, covering the latest syntax and techniques." },
            { title: "Pandas Full Python Course - Data Science Fundamentals", creator: "NeuralNine", date: "2025-01-24", url: "https://www.youtube.com/watch?v=EhYC02PD_gc", focus: "This comprehensive Pandas course includes significant sections on data cleaning and preprocessing." }
        ]
    },
    {
        section: "Feature Engineering",
        intro: "Unlock the predictive power of your data. Feature engineering is the art of creating new, informative features from existing data to improve model performance.",
        videos: [
            { title: "Feature Engineering Techniques For Machine Learning", creator: "Greg Hogg", date: "2021-12-18", url: "https://www.youtube.com/watch?v=GduT2ZCc26E", focus: "Discusses and demonstrates various feature engineering techniques in Python, providing practical code examples." },
            { title: "Feature Engineering Full Course - in 1 Hour", creator: "Analytics Vidhya", date: "2023-02-22", url: "https://www.youtube.com/watch?v=uu8um0JmYA8", focus: "A comprehensive overview of feature engineering concepts and techniques with code examples." }
        ]
    },
    {
        section: "Model Training (From Scratch)",
        intro: "Dive deep into the mechanics of machine learning. These videos guide you through implementing core algorithms from scratch, providing a fundamental understanding of how they learn.",
        videos: [
            { title: "Linear Regression from Scratch & in Scikit learn", creator: "Emmanuel Jesuyon Dansu", date: "2025-05-29", url: "https://www.youtube.com/watch?v=ylU0hVu25nY", focus: "A very recent tutorial that covers implementing linear regression from scratch." },
            { title: "Learn how Matrix Inversion in Linear Regression | 2025", creator: "KD code", date: "2024-12-25", url: "https://www.youtube.com/watch?v=4GkHPUBB9kE", focus: "Focuses on a specific mathematical aspect used in some linear regression implementations." },
            { title: "Logistic Regression From Scratch in Python", creator: "NeuralNine", date: "2025-06-17", url: "https://www.youtube.com/watch?v=S6iuhdYsGC8", focus: "Explains the mathematical concepts and Python implementation of logistic regression from scratch." },
            { title: "How to Implement Logistic Regression in Python", creator: "Greg Hogg", date: "2021-11-01", url: "https://www.youtube.com/watch?v=bfLm584Cz-M", focus: "A longer, more in-depth tutorial for implementing logistic regression from scratch." },
            { title: "K-nearest Neighbors (KNN) in 3 min", creator: "Visually Explained", date: "2025-04-17", url: "https://www.youtube.com/watch?v=gs9E7E0qOIc", focus: "A concise and recent explanation of KNN, covering the core idea to implement." },
            { title: "KNN Algorithm in Machine Learning", creator: "TutorialsPoint", date: "2024-01-24", url: "https://www.youtube.com/watch?v=hlHn7ZH8j8E", focus: "Explains the KNN algorithm with examples, providing the foundation for a from-scratch implementation." },
            { title: "Building a Decision Tree Model – Step-by-Step", creator: "Imarticus Learning", date: "2025-04-24", url: "https://www.youtube.com/watch?v=f5WIoepg2Rw", focus: "This recent video covers building a decision tree model as part of a deployment process." },
            { title: "Decision Tree Tutorial (Scikit-learn)", creator: "paul easywebdesign", date: "2025-02-12", url: "https://www.youtube.com/watch?v=ZqyYZCyPy7k", focus: "Explains the underlying logic and structure of decision trees, necessary for a from-scratch implementation." }
        ]
    },
    {
        section: "Gradient Descent",
        intro: "The optimization engine behind many ML models. Understand how Gradient Descent works to iteratively find the best parameters for your model by minimizing the error.",
        videos: [
            { title: "Machine Learning Lecture #9 - Gradient Descent", creator: "ARTS-Lab at USC", date: "2025-06-13", url: "https://www.youtube.com/watch?v=xUdTFlDas9U", focus: "A recent academic lecture on gradient descent for a thorough understanding." },
            { title: "Gradient Descent From Scratch in Python", creator: "NeuralNine", date: "2023-04-18", url: "https://www.youtube.com/watch?v=gsfbWn4Gy5Q", focus: "Explains gradient descent visually and implements it in Python from scratch." }
        ]
    },
    {
        section: "Model Evaluation",
        intro: "How good is your model? This section covers the metrics used to evaluate model performance, such as accuracy, precision, and recall, which are crucial for understanding a model's effectiveness.",
        videos: [
            { title: "All Machine Learning Models Clearly Explained!", creator: "AI For Beginners", date: "2025-02-02", url: "https://www.youtube.com/watch?v=0YdpwSYMY6I", focus: "Often includes explanations of how different models are evaluated, informing from-scratch metric implementations." },
            { title: "Best trick to remember- Precision & recall formula", creator: "Biomedical AI Basics", date: "2023-11-01", url: "https://www.youtube.com/watch?v=83Gh8XsfZMo", focus: "A quick video specifically on Precision and Recall formulas, key for manual implementation." }
        ]
    },
    {
        section: "Hyperparameter Tuning",
        intro: "Fine-tune your model for peak performance. Learn techniques like Grid Search and Random Search to find the optimal set of hyperparameters for your specific problem.",
        videos: [
            { title: "Grid Search for Hyperparameter Tuning", creator: "Emmanuel Jesuyon Dansu", date: "2025-06-06", url: "https://www.youtube.com/watch?v=wWEi9Lb511U", focus: "A very recent tutorial specifically on Grid Search for hyperparameter tuning." },
            { title: "Grid Search CV & Random Search Explained", creator: "Bluetu Devs", date: "2025-06-10", url: "https://www.youtube.com/watch?v=H-Z4kgDiZE4", focus: "Explains both Grid Search and Random Search, providing the conceptual basis for a from-scratch implementation." },
            { title: "Mastering Hyperparameter Tuning", creator: "SAI Data Science", date: "2025-06-13", url: "https://www.youtube.com/watch?v=tngMamXTAAM", focus: "A recent and potentially more advanced look at hyperparameter tuning." }
        ]
    },
    {
        section: "Deployment",
        intro: "Bring your model to life! Deployment involves making your trained model available for others to use, often by creating a web API. This is the final step in delivering value.",
        videos: [
            { title: "Full Flask Course For Python - Deployment", creator: "NeuralNine", date: "2024-07-21", url: "https://www.youtube.com/watch?v=oQ5UfJqW5Jo", focus: "A comprehensive Flask course that includes deployment, essential for creating an API for your ML model." },
            { title: "Expose Your ML Model with a REST API", creator: "Imarticus Learning", date: "2025-04-25", url: "https://www.youtube.com/watch?v=5TZdsSYT2C8", focus: "A very recent tutorial on exposing ML models via REST APIs using either Flask or FastAPI." },
            { title: "Deploy ML Projects for Free | Render Deployment", creator: "CampusX", date: "2024-07-16", url: "https://www.youtube.com/watch?v=WuEGXlokpuQ", focus: "Shows how to deploy an ML project, which often involves setting up a Flask/FastAPI backend." }
        ]
    }
];

let currentSectionIndex = -1;
let currentSearchTerm = '';
let currentYearFilter = 'All';
let summaryChart = null;

const navigationEl = document.getElementById('navigation');
const gridEl = document.getElementById('resources-grid');
const sectionTitleEl = document.getElementById('section-title');
const sectionIntroEl = document.getElementById('section-intro');
const searchInputEl = document.getElementById('search-input');
const yearFiltersEl = document.getElementById('year-filters');
const chartSectionEl = document.getElementById('chart-section');
const filtersEl = document.getElementById('filters');
const welcomeMessageEl = document.getElementById('welcome-message');

function renderNavigation() {
    navigationEl.innerHTML = '';
    const summaryItem = document.createElement('a');
    summaryItem.href = '#';
    summaryItem.className = `sidebar-item block py-2.5 px-4 rounded font-medium ${currentSectionIndex === -1 ? 'bg-teal-100 text-teal-700' : 'hover:bg-slate-100'}`;
    summaryItem.textContent = 'Dashboard Summary';
    summaryItem.onclick = (e) => {
        e.preventDefault();
        displaySummary();
    };
    navigationEl.appendChild(summaryItem);

    resourcesData.forEach((item, index) => {
        const navItem = document.createElement('a');
        navItem.href = '#';
        navItem.className = `sidebar-item block py-2.5 px-4 rounded font-medium ${index === currentSectionIndex ? 'bg-teal-100 text-teal-700' : 'hover:bg-slate-100'}`;
        navItem.textContent = item.section;
        navItem.onclick = (e) => {
            e.preventDefault();
            displaySection(index);
        };
        navigationEl.appendChild(navItem);
    });
}

function renderResources() {
    gridEl.innerHTML = '';
    if(currentSectionIndex === -1) {
        welcomeMessageEl.classList.remove('hidden');
        gridEl.classList.add('hidden');
        filtersEl.classList.add('hidden');
        return;
    }

    welcomeMessageEl.classList.add('hidden');
    gridEl.classList.remove('hidden');
    filtersEl.classList.remove('hidden');

    const section = resourcesData[currentSectionIndex];
    const filteredVideos = section.videos.filter(video => {
        const searchMatch = currentSearchTerm === '' || 
            video.title.toLowerCase().includes(currentSearchTerm) ||
            video.creator.toLowerCase().includes(currentSearchTerm) ||
            video.focus.toLowerCase().includes(currentSearchTerm);
        
        const yearMatch = currentYearFilter === 'All' || video.date.startsWith(currentYearFilter);

        return searchMatch && yearMatch;
    });

    if (filteredVideos.length === 0) {
        gridEl.innerHTML = `<p class="text-slate-500 col-span-full text-center">No resources match your criteria.</p>`;
        return;
    }

    filteredVideos.forEach(video => {
        const card = document.createElement('div');
        card.className = 'resource-card bg-white p-5 rounded-lg shadow-sm border border-slate-200 flex flex-col';
        card.innerHTML = `
            <h3 class="text-lg font-bold text-slate-900 mb-2">${video.title}</h3>
            <p class="text-sm text-slate-500 mb-1">By: <span class="font-medium text-slate-600">${video.creator}</span></p>
            <p class="text-sm text-slate-500 mb-4">Published: <span class="font-medium text-slate-600">${video.date}</span></p>
            <p class="text-slate-600 text-sm mb-4 flex-grow">${video.focus}</p>
            <a href="${video.url}" target="_blank" rel="noopener noreferrer" class="mt-auto inline-block text-center bg-teal-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-teal-600 transition-colors">
                Watch Video <span class="ml-1">&#128279;</span>
            </a>
        `;
        gridEl.appendChild(card);
    });
}

function displaySection(index) {
    currentSectionIndex = index;
    const section = resourcesData[index];
    sectionTitleEl.textContent = section.section;
    sectionIntroEl.textContent = section.intro;
    chartSectionEl.classList.add('hidden');
    renderNavigation();
    renderResources();
    updateYearFilters();
}

function displaySummary() {
    currentSectionIndex = -1;
    sectionTitleEl.textContent = 'Dashboard Summary';
    sectionIntroEl.textContent = 'An overview of all learning resources. Click a bar on the chart or a topic in the sidebar to dive deeper.';
    chartSectionEl.classList.remove('hidden');
    renderNavigation();
    renderResources();
}

function updateYearFilters() {
    yearFiltersEl.innerHTML = '';
    const allYears = new Set();
    if(currentSectionIndex > -1) {
        resourcesData[currentSectionIndex].videos.forEach(v => allYears.add(v.date.substring(0, 4)));
    } else {
        // In summary view, maybe don't show year filters, or show all possible years.
        // For simplicity, we'll hide them in summary.
    }
    
    const sortedYears = Array.from(allYears).sort((a,b) => b-a);
    
    const createBtn = (year) => {
        const btn = document.createElement('button');
        btn.textContent = year;
        btn.className = `filter-btn px-3 py-1 text-sm rounded-full transition-colors border border-slate-300 ${currentYearFilter === year ? 'active' : 'hover:bg-slate-100'}`;
        btn.onclick = () => {
            currentYearFilter = year;
            updateYearFilters();
            renderResources();
        };
        yearFiltersEl.appendChild(btn);
    };

    createBtn('All');
    sortedYears.forEach(createBtn);
}


function renderSummaryChart() {
    const ctx = document.getElementById('summaryChart').getContext('2d');
    const labels = resourcesData.map(item => item.section);
    const data = resourcesData.map(item => item.videos.length);

    if (summaryChart) {
        summaryChart.destroy();
    }

    summaryChart = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: labels,
            datasets: [{
                label: '# of Videos',
                data: data,
                backgroundColor: 'rgba(20, 184, 166, 0.6)', // teal-500 with opacity
                borderColor: 'rgba(15, 118, 110, 1)', // teal-700
                borderWidth: 1
            }]
        },
        options: {
            indexAxis: 'y',
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                x: {
                    beginAtZero: true,
                    ticks: {
                        stepSize: 1
                    }
                },
                y: {
                    ticks: {
                       autoSkip: false,
                       maxRotation: 0,
                       minRotation: 0,
                    }
                }
            },
            plugins: {
                legend: {
                    display: false
                },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            return ` ${context.dataset.label}: ${context.raw}`;
                        }
                    }
                }
            },
            onClick: (event, elements) => {
                if (elements.length > 0) {
                    const chartElement = elements[0];
                    const index = chartElement.index;
                    displaySection(index);
                }
            }
        }
    });
}

// Initial Load
document.addEventListener('DOMContentLoaded', () => {
    searchInputEl.addEventListener('input', (e) => {
        currentSearchTerm = e.target.value.toLowerCase();
        renderResources();
    });

    renderNavigation();
    renderSummaryChart();
    displaySummary(); // Start with summary view
});

</script>
</body>
</html>
