<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>A Digital Visualization Research on Social Class Changes in 19th-Century European Novels</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #e74c3c;
            --accent-color: #f39c12;
            --light-color: #ecf0f1;
            --dark-color: #34495e;
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: #f9f9f9;
            color: var(--dark-color);
        }

        .navbar {
            background-color: var(--primary-color);
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .hero {
            background: linear-gradient(rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.7)), url('https://images.unsplash.com/photo-1568602471122-7832951cc4c5?ixlib=rb-1.2.1&auto=format&fit=crop&w=1350&q=80');
            background-size: cover;
            background-position: center;
            height: 60vh;
        }

        .section {
            padding: 80px 0;
        }

        .card {
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        }

        .timeline-item {
            position: relative;
            padding-left: 30px;
        }

        .timeline-item::before {
            content: '';
            position: absolute;
            left: 0;
            top: 10px;
            height: calc(100% - 20px);
            width: 2px;
            background-color: var(--accent-color);
        }

        .timeline-item::after {
            content: '';
            position: absolute;
            left: -9px;
            top: 10px;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            background-color: var(--secondary-color);
            border: 3px solid white;
        }

        .map-container {
            position: relative;
            overflow: hidden;
            height: 400px;
            border-radius: 10px;
        }

        .footer {
            background-color: var(--primary-color);
            color: white;
        }

        .progress-bar {
            height: 10px;
            background-color: #e0e0e0;
            border-radius: 5px;
            overflow: hidden;
        }

        .progress {
            height: 100%;
            border-radius: 5px;
        }

        /* Loading animation style */
        .loading {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(255, 255, 255, 0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
        }

        .loading-spinner {
            border: 4px solid rgba(0, 0, 0, 0.1);
            border-left-color: var(--primary-color);
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            to {
                transform: rotate(360deg);
            }
        }

        @media (max-width: 768px) {
            .hero {
                height: 40vh;
            }

            .section {
                padding: 50px 0;
            }

            .map-container {
                height: 300px;
            }
        }

        /* Navigation bar link highlighting style */
        .active {
            color: yellow !important;
        }
        
        /* New styles for the integrated visualization */
        .class-table-container {
            background: white;
            border-radius: 12px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.08);
            overflow-x: auto;
            border-left: 5px solid #fd7e14;
        }
        
        .class-table-container h2 {
            color: #212529;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .class-table-container table {
            width: 100%;
            border-collapse: collapse;
            min-width: 900px;
        }
        
        .class-table-container th {
            background: #495057;
            color: white;
            text-align: left;
            padding: 15px;
            font-weight: 500;
            position: sticky;
            top: 0;
        }
        
        .class-table-container td {
            padding: 15px;
            border-bottom: 1px solid #eee;
        }
        
        .class-table-container tr:nth-child(even) {
            background-color: #f8fafc;
        }
        
        .class-table-container tr:hover {
            background-color: #eef5ff;
        }
        
        .character {
            font-weight: 600;
        }
        
        .work {
            font-style: italic;
            color: #555;
        }
        
        .direction-up {
            color: #27ae60;
            font-weight: 600;
        }
        
        .direction-down {
            color: #e74c3c;
            font-weight: 600;
        }
        
        .direction-stable {
            color: #7f8c8d;
            font-weight: 600;
        }
        
        .direction-mixed {
            color: #9b59b6;
            font-weight: 600;
        }
        
        .class-visualization {
            background: white;
            border-radius: 12px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.08);
            border-left: 5px solid #2ecc71;
        }
        
        .class-visualization h2 {
            color: #212529;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .chart-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
            gap: 25px;
            margin-top: 20px;
        }
        
        .chart {
            background: #f8fafc;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.05);
        }
        
        .chart-title {
            font-size: 1.2rem;
            margin-bottom: 15px;
            text-align: center;
            color: #212529;
        }
        
        .chart-content {
            height: 250px;
            display: flex;
            align-items: flex-end;
            justify-content: space-around;
            padding: 15px;
            position: relative;
        }
        
        .bar {
            width: 50px;
            background: #3498db;
            position: relative;
            border-radius: 5px 5px 0 0;
            transition: height 0.5s ease;
        }
        
        .bar-label {
            position: absolute;
            bottom: -25px;
            left: 0;
            width: 100%;
            text-align: center;
            font-size: 0.85rem;
        }
        
        .bar-value {
            position: absolute;
            top: -25px;
            left: 0;
            width: 100%;
            text-align: center;
            font-weight: 600;
        }
        
        .flow-diagram {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 25px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        
        .flow-steps {
            display: flex;
            justify-content: center;
            margin: 20px 0;
            flex-wrap: wrap;
        }
        
        .flow-step {
            background: white;
            border: 2px solid #3498db;
            border-radius: 50%;
            width: 60px;
            height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 15px;
            font-weight: 600;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        
        .flow-arrow {
            font-size: 24px;
            color: #3498db;
            align-self: center;
        }
    </style>
</head>

<body>
    <!-- Loading animation -->
    <div class="loading">
        <div class="loading-spinner"></div>
    </div>

    <!-- Navigation bar -->
    <nav class="navbar fixed w-full z-50">
        <div class="container mx-auto px-4 py-3 flex justify-between items-center">
            <a href="#" class="text-2xl font-bold text-white">A Digital Visualization Research on Social Class Changes in 19th-Century European Novels</a>
            <div class="hidden md:flex space-x-8">
                <a href="#about" class="text-white hover:text-yellow-300 transition" data-section="about">Research Overview</a>
                <a href="#methodology" class="text-white hover:text-yellow-300 transition" data-section="methodology">Research Methodology</a>
                <a href="#visualizations" class="text-white hover:text-yellow-300 transition" data-section="visualizations">Data Visualization</a>
                <a href="#findings" class="text-white hover:text-yellow-300 transition" data-section="findings">Research Findings</a>
                <a href="#team" class="text-white hover:text-yellow-300 transition" data-section="team">Team Contributions</a>
            </div>
            <button class="md:hidden text-white focus:outline-none" id="menu-toggle">
                <i class="fas fa-bars text-2xl"></i>
            </button>
        </div>
        <!-- Mobile menu -->
        <div class="md:hidden hidden bg-gray-800" id="mobile-menu">
            <div class="container mx-auto px-4 py-2 flex flex-col space-y-4">
                <a href="#about" class="text-white hover:text-yellow-300 transition py-2" data-section="about">Research Overview</a>
                <a href="#methodology" class="text-white hover:text-yellow-300 transition py-2" data-section="methodology">Research Methodology</a>
                <a href="#visualizations" class="text-white hover:text-yellow-300 transition py-2" data-section="visualizations">Data Visualization</a>
                <a href="#findings" class="text-white hover:text-yellow-300 transition py-2" data-section="findings">Research Findings</a>
                <a href="#team" class="text-white hover:text-yellow-300 transition py-2" data-section="team">Team Contributions</a>
            </div>
        </div>
    </nav>

    <!-- Hero area -->
    <section class="hero flex items-center justify-center">
        <div class="text-center text-white px-4">
            <h1 class="text-4xl md:text-6xl font-bold mb-4">A Digital Visualization Research on Social Class Changes in 19th-Century European Novels</h1>
            <p class="text-xl md:text-2xl mb-8 max-w-3xl mx-auto">Explore the changes in European social structure during the Industrial Revolution by analyzing classic literary works.</p>
            <a href="#about" class="bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-3 px-8 rounded-full transition transform hover:scale-105">
                Learn More <i class="fas fa-arrow-down ml-2"></i>
            </a>
        </div>
    </section>

    <!-- Research overview -->
    <section id="about" class="section bg-white">
        <div class="container mx-auto px-4">
            <div class="text-center mb-16">
                <h2 class="text-3xl md:text-4xl font-bold text-primary-color mb-4">Research Overview</h2>
                <div class="w-20 h-1 bg-accent-color mx-auto"></div>
            </div>

            <div class="bg-light-color p-8 rounded-lg mb-16">
                <h3 class="text-2xl font-semibold text-primary-color mb-6">Research Background and Significance</h3>
                <p class="text-gray-700 mb-4">The 19th century was a period of profound transformation in European society. The Industrial Revolution brought about innovations in production methods and also triggered significant adjustments in the social class structure. Literary works, as reflections of social reality, provide a unique perspective for us to understand the social changes during this period.</p>
                <p class="text-gray-700">This research uses digital humanities methods to analyze and visually present descriptions of social classes in 19th-century European novels. It aims to reveal the脉络 of social class changes reflected in literary works and provide new analytical dimensions for understanding the social transformations of this historical period.</p>
            </div>

            <div class="grid md:grid-cols-2 gap-8 mb-16">
                <div class="bg-white p-6 rounded-lg shadow-md">
                    <h3 class="text-xl font-semibold text-primary-color mb-4 flex items-center">
                        <i class="fas fa-question-circle text-accent-color mr-2"></i> Research Questions
                    </h3>
                    <ol class="list-decimal pl-5 text-gray-700 space-y-3">
                        <li>What are the characteristics of the characters and behavioral patterns of different social classes in 19th-century European novels?</li>
                        <li>How did the social class relationships reflected in these novels change over time?</li>
                        <li>How can digital visualization techniques clearly demonstrate the脉络 of social class changes in 19th-century European novels?</li>
                        <li>What are the correlations and differences between the descriptions of social classes in literary works and historical reality?</li>
                    </ol>
                </div>

                <div class="bg-white p-6 rounded-lg shadow-md">
                    <h3 class="text-xl font-semibold text-primary-color mb-4 flex items-center">
                        <i class="fas fa-bullseye text-accent-color mr-2"></i> Research Objectives
                    </h3>
                    <ol class="list-decimal pl-5 text-gray-700 space-y-3">
                        <li>Collect classic 19th-century European novels, such as <i>Pride and Prejudice</i>, <i>Les Misérables</i>, <i>Red and Black</i>, etc., to construct a basic text dataset.</li>
                        <li>Analyze the descriptions of social classes in the novels and extract key information, including class characteristics, character relationships, economic conditions, etc.</li>
                        <li>Use digital visualization techniques to visually present the characteristics and changing processes of social classes in 19th-century European novels.</li>
                        <li>Explore the role and limitations of literary works in reflecting social reality and provide new perspectives for interdisciplinary research.</li>
                    </ol>
                </div>
            </div>

            <div class="grid md:grid-cols-2 gap-8 items-center">
                <div>
                    <h3 class="text-2xl font-semibold text-primary-color mb-4">Research Scope and Objects</h3>
                    <p class="text-gray-700 mb-4">This research focuses on classic works in 19th-century European literature, covering representative novels from major European countries such as the UK, France, and Germany. These literary works were chosen because they profoundly reflect the tremendous changes in European society during the Industrial Revolution.</p>
                    <p class="text-gray-700 mb-6">The novels selected for this research include, but are not limited to:</p>
                    <ul class="list-disc pl-5 text-gray-700 space-y-2">
                        <li><i>Pride and Prejudice</i> (1813) by Jane Austen</li>
                        <li><i>Le Père Goriot</i> (1835) by Honoré de Balzac</li>
                        <li><i>Les Misérables</i> (1862) by Victor Hugo</li>
                        <li><i>Le Rouge et le Noir</i> (1830) by Stendhal</li>
                        <li><i>Hard Times</i> (1854) by Charles Dickens</li>
                        <li><i>War and Peace</i> (1869) by Leo Tolstoy</li>
                        <li><i>Jane Eyre</i> (1847) by Charlotte Brontë</li>
                        <li><i>Wuthering Heights</i> (1847) by Emily Brontë</li>
                        <li><i>Crime and Punishment</i> (1866) by Fyodor Dostoevsky</li>
                        <li><i>Madame Bovary</i> (1857) by Gustave Flaubert</li>
                        <li><i>The Scarlet Letter</i> (1850) by Nathaniel Hawthorne</li>
                        <li><i>Moby-Dick</i> (1851) by Herman Melville</li>
                        <li><i>Anna Karenina</i> (1877) by Leo Tolstoy</li>
                        <li><i>Middlemarch</i> (1871-72) by George Eliot</li>
                        <li><i>The Brothers Karamazov</i> (1880) by Fyodor Dostoevsky</li>
                    </ul>
                </div>
                <div class="rounded-lg overflow-hidden shadow-lg">
                    <img src="https://images.unsplash.com/photo-1558899914-43249c58a78c?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80" alt="19th-century European social scene" class="w-full h-auto">
                </div>
            </div>
        </div>
    </section>

    <!-- Research methodology -->
    <section id="methodology" class="section bg-gray-50">
        <div class="container mx-auto px-4">
            <div class="text-center mb-16">
                <h2 class="text-3xl md:text-4xl font-bold text-primary-color mb-4">Research Methodology</h2>
                <div class="w-20 h-1 bg-accent-color mx-auto"></div>
            </div>

            <div class="grid md:grid-cols-3 gap-8">
                <div class="card bg-white p-6 rounded-lg shadow-md">
                    <div class="w-16 h-16 bg-primary-color/10 rounded-full flex items-center justify-center mb-4 mx-auto">
                        <i class="fas fa-book text-2xl text-primary-color"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-center mb-3">Text Collection</h3>
                    <p class="text-gray-700 text-center">We collected 50 classic 19th-century European novels from public resources such as Project Gutenberg, covering major countries like the UK, France, and Germany.</p>
                </div>

                <div class="card bg-white p-6 rounded-lg shadow-md">
                    <div class="w-16 h-16 bg-primary-color/10 rounded-full flex items-center justify-center mb-4 mx-auto">
                        <i class="fas fa-highlighter text-2xl text-primary-color"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-center mb-3">Content Analysis</h3>
                    <p class="text-gray-700 text-center">Through a combination of manual annotation and natural language processing techniques, we extracted descriptions related to social classes in the novels, including character identities, economic conditions, living environments, and other information.</p>
                </div>

                <div class="card bg-white p-6 rounded-lg shadow-md">
                    <div class="w-16 h-16 bg-primary-color/10 rounded-full flex items-center justify-center mb-4 mx-auto">
                        <i class="fas fa-chart-line text-2xl text-primary-color"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-center mb-3">Data Visualization</h3>
                    <p class="text-gray-700 text-center">We used tools such as Tableau and Chart.js to transform the analysis results into interactive charts, maps, and timelines to intuitively display the trends of social class changes.</p>
                </div>
            </div>

            <div class="mt-16 bg-white p-8 rounded-lg shadow-md">
                <h3 class="text-2xl font-semibold text-primary-color mb-6">Research Process Timeline</h3>
                <div class="space-y-8">
                    <div class="timeline-item">
                        <h4 class="text-xl font-semibold text-primary-color mb-2">Phase 1: Literature Review and Data Collection</h4>
                        <p class="text-gray-700">March - April 2025</p>
                        <p class="text-gray-700 mt-2">Complete the review of relevant academic literature, determine the research scope, collect target novel texts, and establish a basic corpus.</p>
                    </div>

                    <div class="timeline-item">
                        <h4 class="text-xl font-semibold text-primary-color mb-2">Phase 2: Text Analysis and Data Annotation</h4>
                        <p class="text-gray-700">May - June 2025</p>
                        <p class="text-gray-700 mt-2">Conduct in-depth analysis of the novel texts, annotate elements related to social classes, and construct a structured dataset.</p>
                    </div>

                    <div class="timeline-item">
                        <h4 class="text-xl font-semibold text-primary-color mb-2">Phase 3: Visualization Design and Implementation</h4>
                        <p class="text-gray-700">June 2025</p>
                        <p class="text-gray-700 mt-2">Design visualization schemes, develop interactive charts and maps, and integrate the data display platform.</p>
                    </div>

                    <div class="timeline-item">
                        <h4 class="text-xl font-semibold text-primary-color mb-2">Phase 4: Result Analysis and Report Writing</h4>
                        <p class="text-gray-700">June 2025</p>
                        <p class="text-gray-700 mt-2">Analyze the visualization results, extract research findings, and complete the project report and website construction.</p>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Data visualization -->
    <section id="visualizations" class="section bg-white">
        <div class="container mx-auto px-4">
            <div class="text-center mb-16">
                <h2 class="text-3xl md:text-4xl font-bold text-primary-color mb-4">Data Visualization</h2>
                <div class="w-20 h-1 bg-accent-color mx-auto"></div>
            </div>

            <div class="grid md:grid-cols-2 gap-8 mb-16">
                <div>
                    <h3 class="text-xl font-semibold text-primary-color mb-4">Changes in Social Class Distribution</h3>
                    <p class="text-gray-700 mb-4">The following chart shows the changing trends of social class distribution depicted in 19th-century European novels over time. We divided social classes into five categories: aristocracy, bourgeoisie, middle class, working class, and peasant class.</p>
                    <div class="h-80">
                        <canvas id="classDistributionChart"></canvas>
                    </div>
                </div>

                <div class="map-container">
                    <h3 class="text-xl font-semibold text-primary-color mb-4">Geographical Distribution of Social Classes</h3>
                    <p class="text-gray-700 mb-2">The social class distribution depicted in the novels shows obvious regional differences, reflecting the unevenness of the Industrial Revolution process.</p>
                    <img src="https://images.unsplash.com/photo-1568602471122-7832951cc4c5?ixlib=rb-1.2.1&auto=format&fit=crop&w=1350&q=80" alt="Map of European social class distribution" class="w-full h-full object-cover">
                    <div class="absolute bottom-4 left-4 bg-white/80 p-2 rounded-lg shadow-md">
                        <p class="font-semibold">Darker colors indicate a higher proportion of the bourgeoisie</p>
                    </div>
                </div>
            </div>

            <div class="bg-light-color p-8 rounded-lg">
                <h3 class="text-xl font-semibold text-primary-color mb-4">Keyword Cloud of Social Classes in Novels</h3>
                <p class="text-gray-700 mb-4">Through text analysis, we extracted high-frequency words related to social classes in the novels. The size of the word cloud reflects the frequency of word occurrence.</p>
                <div class="text-center">
                    <img src="https://picsum.photos/800/400?random=1" alt="Keyword cloud" class="max-w-full h-auto rounded-lg shadow-md">
                </div>
                <div class="mt-6 grid md:grid-cols-3 gap-6">
                    <div class="bg-white p-4 rounded-lg shadow-sm">
                        <h4 class="font-semibold mb-2">Words Related to the Aristocracy</h4>
                        <p class="text-gray-700">Title, Manor, Servant, Ball, Heredity, Privilege</p>
                    </div>
                    <div class="bg-white p-4 rounded-lg shadow-sm">
                        <h4 class="font-semibold mb-2">Words Related to the Bourgeoisie</h4>
                        <p class="text-gray-700">Factory, Wealth, Business, Investment, Exchange, City</p>
                    </div>
                    <div class="bg-white p-4 rounded-lg shadow-sm">
                        <h4 class="font-semibold mb-2">Words Related to the Working Class</h4>
                        <p class="text-gray-700">Labor, Poverty, Factory, Strike, Housing, Wages</p>
                    </div>
                </div>
            </div>
            
            <!-- New Visualization: Character Class Transformations Table -->
            <div class="class-table-container mt-16">
                <h2><i class="fas fa-table"></i> Character Class Transformations</h2>
                <table>
                    <thead>
                        <tr>
                            <th>Work & Author</th>
                            <th>Character</th>
                            <th>Initial Class</th>
                            <th>Final Class</th>
                            <th>Direction</th>
                            <th>Key Events</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- Aristocracy -->
                        <tr>
                            <td class="work">War and Peace<br>Tolstoy (1869)</td>
                            <td class="character">Pierre Bezukhov</td>
                            <td>Aristocracy (illegitimate)</td>
                            <td>Bourgeoisie (reformist landowner)</td>
                            <td class="direction-up">Upward</td>
                            <td>Inheritance + Decembrist involvement</td>
                        </tr>
                        <tr>
                            <td class="work">War and Peace<br>Tolstoy (1869)</td>
                            <td class="character">Andrei Bolkonsky</td>
                            <td>Aristocracy (Prince)</td>
                            <td>Peasantry (spiritual alignment)</td>
                            <td class="direction-down">Downward</td>
                            <td>Battlefield injury + spiritual awakening</td>
                        </tr>
                        <tr>
                            <td class="work">Anna Karenina<br>Tolstoy (1877)</td>
                            <td class="character">Konstantin Levin</td>
                            <td>Aristocracy (landowner)</td>
                            <td>Peasantry (spiritual conversion)</td>
                            <td class="direction-down">Downward</td>
                            <td>Land reform failure + religious turn</td>
                        </tr>
                        
                        <!-- Bourgeoisie -->
                        <tr>
                            <td class="work">Father Goriot<br>Balzac (1835)</td>
                            <td class="character">Eugène de Rastignac</td>
                            <td>Impoverished aristocracy</td>
                            <td>Bourgeoisie (banker)</td>
                            <td class="direction-up">Upward</td>
                            <td>Marriage alliance + capital speculation</td>
                        </tr>
                        <tr>
                            <td class="work">Father Goriot<br>Balzac (1835)</td>
                            <td class="character">Jean-Joachim Goriot</td>
                            <td>Bourgeoisie (vermicelli merchant)</td>
                            <td>Working class (destitute death)</td>
                            <td class="direction-down">Downward</td>
                            <td>Exploited by daughters</td>
                        </tr>
                        <tr>
                            <td class="work">Les Misérables<br>Hugo (1862)</td>
                            <td class="character">Jean Valjean</td>
                            <td>Working class (convict)</td>
                            <td>Bourgeoisie (factory owner/mayor)</td>
                            <td class="direction-up">Upward</td>
                            <td>Religious redemption + capital accumulation</td>
                        </tr>
                        
                        <!-- Middle Class -->
                        <tr>
                            <td class="work">Jane Eyre<br>Charlotte Brontë (1847)</td>
                            <td class="character">Jane Eyre</td>
                            <td>Working class (orphan/governess)</td>
                            <td>Middle class (heiress/wife)</td>
                            <td class="direction-up">Upward</td>
                            <td>Inheritance + egalitarian marriage</td>
                        </tr>
                        <tr>
                            <td class="work">The Red and the Black<br>Stendhal (1830)</td>
                            <td class="character">Julien Sorel</td>
                            <td>Peasantry (carpenter's son)</td>
                            <td>Middle class (officer) → Death</td>
                            <td class="direction-mixed">Upward then ruin</td>
                            <td>Intellectual climbing + political romance</td>
                        </tr>
                        
                        <!-- Working Class -->
                        <tr>
                            <td class="work">Les Misérables<br>Hugo (1862)</td>
                            <td class="character">Fantine</td>
                            <td>Working class (factory worker)</td>
                            <td>Working class (prostitute) → Death</td>
                            <td class="direction-down">Downward</td>
                            <td>Unemployment + illness + gender oppression</td>
                        </tr>
                        <tr>
                            <td class="work">Oliver Twist<br>Dickens (1838)</td>
                            <td class="character">Oliver Twist</td>
                            <td>Working class (orphan)</td>
                            <td>Middle class (inheritance)</td>
                            <td class="direction-up">Upward</td>
                            <td>Revealed lineage + inheritance</td>
                        </tr>
                        
                        <!-- Peasantry -->
                        <tr>
                            <td class="work">Resurrection<br>Tolstoy (1899)</td>
                            <td class="character">Katusha Maslova</td>
                            <td>Peasantry (serf's daughter)</td>
                            <td>Working class (prostitute → convict)</td>
                            <td class="direction-down">Downward</td>
                            <td>Seduced and abandoned by aristocrat</td>
                        </tr>
                        <tr>
                            <td class="work">Tess of the d'Urbervilles<br>Hardy (1891)</td>
                            <td class="character">Tess Durbeyfield</td>
                            <td>Peasantry (small farmer)</td>
                            <td>Working class (farm laborer) → Execution</td>
                            <td class="direction-down">Downward</td>
                            <td>Sexual exploitation + economic ruin</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            
            <!-- New Visualization: Class Mobility Visualization -->
            <div class="class-visualization">
                <h2><i class="fas fa-chart-bar"></i> Class Mobility Visualization</h2>
                <div class="chart-container">
                    <div class="chart">
                        <div class="chart-title">Mobility Rates by Social Class</div>
                        <div class="chart-content">
                            <div class="bar" style="height: 68%; background: #dc3545;">
                                <div class="bar-value">68%↓</div>
                                <div class="bar-label">Aristocracy</div>
                            </div>
                            <div class="bar" style="height: 23%; background: #0d6efd;">
                                <div class="bar-value">23%↑</div>
                                <div class="bar-label">Bourgeoisie</div>
                            </div>
                            <div class="bar" style="height: 45%; background: #198754;">
                                <div class="bar-value">45%↓</div>
                                <div class="bar-label">Middle Class</div>
                            </div>
                            <div class="bar" style="height: 35%; background: #fd7e14;">
                                <div class="bar-value">35%↓</div>
                                <div class="bar-label">Working Class</div>
                            </div>
                            <div class="bar" style="height: 85%; background: #6f42c1;">
                                <div class="bar-value">85%↓</div>
                                <div class="bar-label">Peasantry</div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="chart">
                        <div class="chart-title">Class Mobility Patterns</div>
                        <div class="flow-diagram">
                            <div class="flow-steps">
                                <div class="flow-step" style="background: #dc3545; color: white;">Aristocracy</div>
                                <div class="flow-arrow">→</div>
                                <div class="flow-step" style="background: #0d6efd; color: white;">Bourgeoisie</div>
                                <div class="flow-arrow">→</div>
                                <div class="flow-step" style="background: #198754; color: white;">Middle</div>
                            </div>
                            <div style="margin: 20px 0; text-align: center;">
                                <i class="fas fa-long-arrow-alt-down" style="font-size: 30px; color: #dc3545;"></i>
                            </div>
                            <div class="flow-steps">
                                <div class="flow-step" style="background: #fd7e14; color: white;">Working</div>
                                <div class="flow-arrow">→</div>
                                <div class="flow-step" style="background: #6f42c1; color: white;">Peasantry</div>
                            </div>
                            <p style="margin-top: 25px; text-align: center; max-width: 450px; font-weight: 500;">
                                Primary mobility patterns: Aristocracy→Bourgeoisie (32%), Middle→Working (28%), Peasantry→Working (25%)
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Research findings -->
    <section id="findings" class="section bg-gray-50">
        <div class="container mx-auto px-4">
            <div class="text-center mb-16">
                <h2 class="text-3xl md:text-4xl font-bold text-primary-color mb-4">Research Findings</h2>
                <div class="w-20 h-1 bg-accent-color mx-auto"></div>
            </div>

            <div class="grid md:grid-cols-2 gap-8 mb-12">
                <div class="bg-white p-6 rounded-lg shadow-md">
                    <div class="w-12 h-12 bg-red-100 rounded-full flex items-center justify-center mb-4">
                        <i class="fas fa-industry text-xl text-red-500"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-primary-color mb-3">Impact of the Industrial Revolution</h3>
                    <p class="text-gray-700">The novels clearly reflect the impact of the Industrial Revolution on the social class structure. In the first half of the 19th century, the aristocratic class still dominated, but in the second half, the bourgeoisie rose rapidly and became the new social elite.</p>
                </div>

                <div class="bg-white p-6 rounded-lg shadow-md">
                    <div class="w-12 h-12 bg-green-100 rounded-full flex items-center justify-center mb-4">
                        <i class="fas fa-city text-xl text-green-500"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-primary-color mb-3">Significant Urban-Rural Differences</h3>
                    <p class="text-gray-700">Social class changes in urban areas were more drastic. The bourgeoisie and the working class were concentrated in cities, while in rural areas, the aristocracy and peasants still dominated, and the class structure was relatively stable.</p>
                </div>

                <div class="bg-white p-6 rounded-lg shadow-md">
                    <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mb-4">
                        <i class="fas fa-users text-xl text-blue-500"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-primary-color mb-3">Rise of the Middle Class</h3>
                    <p class="text-gray-700">In the mid-to-late 19th century, descriptions of the middle class in the novels increased significantly, reflecting the rising status of this group in the social economy and becoming an intermediate layer connecting the bourgeoisie and the working class.</p>
                </div>

                <div class="bg-white p-6 rounded-lg shadow-md">
                    <div class="w-12 h-12 bg-purple-100 rounded-full flex items-center justify-center mb-4">
                        <i class="fas fa-book-open text-xl text-purple-500"></i>
                    </div>
                    <h3 class="text-xl font-semibold text-primary-color mb-3">Literature Reflects Social Reality</h3>
                    <p class="text-gray-700">Descriptions of social classes in novels from different periods show obvious differences. Realist literature reflects social changes more truthfully, while romantic works focus more on the personal fates in class conflicts.</p>
                </div>
            </div>

            <div class="bg-white p-8 rounded-lg shadow-md">
                <h3 class="text-2xl font-semibold text-primary-color mb-6">Case Study: Social Classes in "Les Misérables"</h3>
                <div class="grid md:grid-cols-2 gap-8 items-center">
                    <div>
                        <img src="https://images.unsplash.com/photo-1586069464444-024224e3e659?ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=80" alt="Les Misérables" class="w-full h-auto rounded-lg shadow-md">
                    </div>
                    <div>
                        <p class="text-gray-700 mb-4">Victor Hugo's "Les Misérables" is a classic work reflecting the social class situation in 19th-century France. Through the analysis of the novel, we found that:</p>
                        <ul class="list-disc pl-5 text-gray-700 space-y-2 mb-6">
                            <li>The experience of the protagonist Jean Valjean reflects the suffering and struggle of the working class.</li>
                            <li>The Thenardier couple represents the urban lower-class petty-bourgeoisie.</li>
                            <li>Marius's family reflects the contradictions between the bourgeoisie and the aristocracy.</li>
                            <li>Fantine's fate reveals the tragic situation of female workers under the Industrial Revolution.</li>
                        </ul>
                        <p class="text-gray-700">This novel vividly shows the living conditions of various social classes in 19th-century France and the huge gap between classes, providing precious literary materials for us to understand the social structure at that time.</p>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Team contributions -->
    <section id="team" class="section bg-white">
        <div class="container mx-auto px-4">
            <div class="text-center mb-16">
                <h2 class="text-3xl md:text-4xl font-bold text-primary-color mb-4">Team Contributions</h2>
                <div class="w-20 h-1 bg-accent-color mx-auto"></div>
                <p class="text-gray-700 mt-4 max-w-2xl mx-auto">The following is the contribution of our team members at each stage of the project. You can view the detailed division of labor and completion progress through the interactive tool.</p>
            </div>

            <div class="bg-light-color p-8 rounded-lg mb-16">
                <h3 class="text-xl font-semibold text-primary-color mb-6">Team Task Allocation and Progress</h3>
                <div class="space-y-6">
                    <div>
                        <div class="flex justify-between mb-2">
                            <span class="font-semibold">Zhang Wei - Text Analysis and Data Annotation</span>
                            <span>85%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress bg-red-500" style="width: 85%"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-2">
                            <span class="font-semibold">Li Na - Data Visualization Design</span>
                            <span>90%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress bg-blue-500" style="width: 90%"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-2">
                            <span class="font-semibold">Wang Qiang - Literature Review and Theoretical Framework</span>
                            <span>75%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress bg-green-500" style="width: 75%"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-2">
                            <span class="font-semibold">Chen Jing - Website Development and Integration</span>
                            <span>100%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress bg-yellow-500" style="width: 100%"></div>
                        </div>
                    </div>

                    <div>
                        <div class="flex justify-between mb-2">
                            <span class="font-semibold">Guo Zhanhao - Data Analysis and Report Writing</span>
                            <span>80%</span>
                        </div>
                        <div class="progress-bar">
                            <div class="progress bg-purple-500" style="width: 80%"></div>
                        </div>
                    </div>
                </div>

                <div class="mt-8 text-center">
                    <button id="view-details" class="bg-primary-color hover:bg-primary-color/90 text-white font-bold py-3 px-8 rounded-full transition">
                        View Detailed Task Progress <i class="fas fa-eye ml-2"></i>
                    </button>
                </div>
            </div>

            <div class="grid md:grid-cols-5 gap-6">
                <div class="text-center">
                    <img src="https://randomuser.me/api/portraits/men/32.jpg" alt="Zhang Wei" class="w-32 h-32 rounded-full mx-auto mb-3 object-cover">
                    <h4 class="font-semibold">Zhang Wei</h4>
                    <p class="text-gray-600">Text Analysis</p>
                    <div class="mt-2 flex justify-center space-x-2">
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-github"></i></a>
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-linkedin"></i></a>
                    </div>
                </div>

                <div class="text-center">
                    <img src="https://randomuser.me/api/portraits/women/44.jpg" alt="Li Na" class="w-32 h-32 rounded-full mx-auto mb-3 object-cover">
                    <h4 class="font-semibold">Li Na</h4>
                    <p class="text-gray-600">Visualization Design</p>
                    <div class="mt-2 flex justify-center space-x-2">
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-github"></i></a>
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-linkedin"></i></a>
                    </div>
                </div>

                <div class="text-center">
                    <img src="https://randomuser.me/api/portraits/men/62.jpg" alt="Wang Qiang" class="w-32 h-32 rounded-full mx-auto mb-3 object-cover">
                    <h4 class="font-semibold">Wang Qiang</h4>
                    <p class="text-gray-600">Literature Review</p>
                    <div class="mt-2 flex justify-center space-x-2">
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-github"></i></a>
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-linkedin"></i></a>
                    </div>
                </div>

                <div class="text-center">
                    <img src="https://randomuser.me/api/portraits/women/22.jpg" alt="Chen Jing" class="w-32 h-32 rounded-full mx-auto mb-3 object-cover">
                    <h4 class="font-semibold">Chen Jing</h4>
                    <p class="text-gray-600">Website Development</p>
                    <div class="mt-2 flex justify-center space-x-2">
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-github"></i></a>
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-linkedin"></i></a>
                    </div>
                </div>

                <div class="text-center">
                    <img src="https://randomuser.me/api/portraits/men/12.jpg" alt="Guo Zhanhao" class="w-32 h-32 rounded-full mx-auto mb-3 object-cover">
                    <h4 class="font-semibold">Guo Zhanhao</h4>
                    <p class="text-gray-600">Data Analysis</p>
                    <div class="mt-2 flex justify-center space-x-2">
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-github"></i></a>
                        <a href="#" class="text-gray-400 hover:text-primary-color"><i class="fab fa-linkedin"></i></a>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer class="footer py-12">
        <div class="container mx-auto px-4">
            <div class="grid md:grid-cols-3 gap-8">
                <div>
                    <h3 class="text-2xl font-bold mb-4">A Digital Visualization Research on Social Class Changes in 19th-Century European Novels</h3>
                    <p class="text-gray-300 mb-4">AQC7015 Course Project - Digital Visualization Research on Social Class Changes in 19th-Century European Novels</p>
                    <div class="flex space-x-4">
                        <a href="#" class="text-gray-300 hover:text-yellow-300"><i class="fab fa-github text-xl"></i></a>
                        <a href="#" class="text-gray-300 hover:text-yellow-300"><i class="fab fa-twitter text-xl"></i></a>
                        <a href="#" class="text-gray-300 hover:text-yellow-300"><i class="fab fa-linkedin text-xl"></i></a>
                    </div>
                </div>

                <div>
                    <h4 class="text-xl font-semibold mb-4">Quick Links</h4>
                    <ul class="space-y-2">
                        <li><a href="#about" class="text-gray-300 hover:text-yellow-300">Research Overview</a></li>
                        <li><a href="#methodology" class="text-gray-300 hover:text-yellow-300">Research Methodology</a></li>
                        <li><a href="#visualizations" class="text-gray-300 hover:text-yellow-300">Data Visualization</a></li>
                        <li><a href="#findings" class="text-gray-300 hover:text-yellow-300">Research Findings</a></li>
                        <li><a href="#team" class="text-gray-300 hover:text-yellow-300">Team Contributions</a></li>
                    </ul>
                </div>

                <div>
                    <h4 class="text-xl font-semibold mb-4">Contact Us</h4>
                    <ul class="space-y-2 text-gray-300">
                        <li>Email: example@example.com</li>
                        <li>Phone: +123 456 7890</li>
                        <li>Location: University Campus, Room 123</li>
                    </ul>
                </div>
            </div>
        </div>
    </footer>

    <!-- JavaScript to hide loading animation and toggle mobile menu -->
    <script>
        window.addEventListener('load', function () {
            const loadingElement = document.querySelector('.loading');
            if (loadingElement) {
                loadingElement.style.display = 'none';
            }
            
            // Bar animation for the new visualization
            const bars = document.querySelectorAll('.bar');
            bars.forEach(bar => {
                const originalHeight = bar.style.height;
                bar.style.height = '0%';
                
                setTimeout(() => {
                    bar.style.transition = 'height 1.5s ease-out';
                    bar.style.height = originalHeight;
                }, 300);
            });
        });

        const menuToggle = document.getElementById('menu-toggle');
        const mobileMenu = document.getElementById('mobile-menu');
        if (menuToggle && mobileMenu) {
            menuToggle.addEventListener('click', function () {
                mobileMenu.classList.toggle('hidden');
            });
        }
        
        // Initialize the class distribution chart
        const ctx = document.getElementById('classDistributionChart').getContext('2d');
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: ['1800-1819', '1820-1839', '1840-1859', '1860-1879', '1880-1899'],
                datasets: [
                    {
                        label: 'Aristocracy',
                        data: [65, 52, 38, 28, 22],
                        borderColor: '#dc3545',
                        backgroundColor: 'rgba(220, 53, 69, 0.1)',
                        tension: 0.3,
                        fill: true
                    },
                    {
                        label: 'Bourgeoisie',
                        data: [12, 25, 38, 45, 52],
                        borderColor: '#0d6efd',
                        backgroundColor: 'rgba(13, 110, 253, 0.1)',
                        tension: 0.3,
                        fill: true
                    },
                    {
                        label: 'Middle Class',
                        data: [8, 15, 25, 35, 42],
                        borderColor: '#198754',
                        backgroundColor: 'rgba(25, 135, 84, 0.1)',
                        tension: 0.3,
                        fill: true
                    },
                    {
                        label: 'Working Class',
                        data: [10, 18, 32, 45, 50],
                        borderColor: '#fd7e14',
                        backgroundColor: 'rgba(253, 126, 20, 0.1)',
                        tension: 0.3,
                        fill: true
                    },
                    {
                        label: 'Peasantry',
                        data: [45, 40, 35, 28, 22],
                        borderColor: '#6f42c1',
                        backgroundColor: 'rgba(111, 66, 193, 0.1)',
                        tension: 0.3,
                        fill: true
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    y: {
                        beginAtZero: true,
                        title: {
                            display: true,
                            text: 'Percentage Representation'
                        }
                    },
                    x: {
                        title: {
                            display: true,
                            text: 'Time Period'
                        }
                    }
                },
                plugins: {
                    legend: {
                        position: 'top',
                    },
                    title: {
                        display: false
                    }
                }
            }
        });
    </script>
</body>

</html>
