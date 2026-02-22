<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Autonomy360 - Enterprise Automation Platform</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --primary-purple: #5B21B6;
            --primary-purple-light: #7C3AED;
            --accent-blue: #3B82F6;
            --purple-light: #C4B5FD;
            --purple-lighter: #EDE9FE;
            --purple-dark: #3F0F7F;
            --white: #FFFFFF;
            --gray-light: #F5F3FF;
            --gray-darker: #1F2937;
            --shadow-sm: 0 2px 12px rgba(91, 33, 182, 0.12);
            --shadow-md: 0 8px 24px rgba(91, 33, 182, 0.15);
            --shadow-lg: 0 16px 48px rgba(91, 33, 182, 0.2);
            --shadow-glow: 0 0 24px rgba(124, 58, 237, 0.25);
        }

        body {
            font-family: 'Poppins', 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
            background: linear-gradient(180deg, #FFFFFF 0%, #F8F6FF 50%, #F0EBFF 100%);
            color: var(--gray-darker);
            line-height: 1.6;
            min-height: 100vh;
        }

        /* LOGIN MODAL */
        .login-modal {
            display: flex;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .login-modal.hidden {
            display: none;
        }

        .login-container {
            background: linear-gradient(135deg, var(--white) 0%, #F9F7FF 100%);
            padding: 48px;
            border-radius: 20px;
            box-shadow: var(--shadow-lg), inset 0 1px 0 rgba(139, 92, 246, 0.1);
            max-width: 420px;
            width: 90%;
            animation: slideUp 0.3s ease-out;
            border: 1px solid rgba(168, 85, 247, 0.1);
        }

        @keyframes slideUp {
            from {
                opacity: 0;
                transform: translateY(20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .login-header {
            margin-bottom: 32px;
            text-align: center;
        }

        .login-header h1 {
            color: var(--primary-purple);
            font-size: 28px;
            margin-bottom: 8px;
            font-weight: 700;
            letter-spacing: -0.5px;
        }

        .login-header p {
            color: #7C3AED;
            font-size: 14px;
            font-weight: 500;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
            color: var(--gray-darker);
            font-weight: 500;
            font-size: 14px;
        }

        .form-group input {
            width: 100%;
            padding: 12px 16px;
            border: 2px solid #D8BFD8;
            border-radius: 10px;
            font-size: 14px;
            transition: all 0.3s ease;
            background: linear-gradient(135deg, #FAFAF9 0%, #F3F0FF 100%);
        }

        .form-group input:focus {
            outline: none;
            border-color: var(--primary-purple);
            background: var(--white);
            box-shadow: 0 0 0 4px rgba(109, 40, 217, 0.15), var(--shadow-glow);
        }

        .login-button {
            width: 100%;
            padding: 12px 24px;
            background: linear-gradient(135deg, var(--primary-purple) 0%, #7C3AED 100%);
            color: var(--white);
            border: none;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 16px;
            box-shadow: 0 4px 15px rgba(109, 40, 217, 0.3);
        }

        .login-button:hover {
            background: linear-gradient(135deg, var(--purple-dark) 0%, #6D28D9 100%);
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(109, 40, 217, 0.4);
        }

        .login-button:active {
            transform: translateY(0);
        }

        .error-message {
            color: #EF4444;
            font-size: 13px;
            margin-top: 8px;
            display: none;
        }

        .error-message.show {
            display: block;
        }

        /* HEADER */
        header {
            background: linear-gradient(135deg, var(--white) 0%, #F9F7FF 100%);
            padding: 16px 32px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 2px 12px rgba(109, 40, 217, 0.08);
            position: sticky;
            top: 0;
            z-index: 100;
            border-bottom: 1px solid rgba(168, 85, 247, 0.1);
        }

        .logo {
            font-size: 20px;
            font-weight: 800;
            color: var(--primary-purple);
            letter-spacing: -1px;
        }

        .logo-svg {
            width: 48px;
            height: 48px;
            display: inline-block;
            margin-right: 12px;
            vertical-align: middle;
        }

        .logo-text {
            display: inline-block;
            vertical-align: middle;
            font-size: 18px;
            font-weight: 800;
            background: linear-gradient(135deg, var(--primary-purple) 0%, #7C3AED 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            letter-spacing: -0.5px;
        }

        .nav-center {
            display: flex;
            gap: 32px;
        }

        .nav-center a {
            text-decoration: none;
            color: var(--gray-darker);
            font-size: 14px;
            font-weight: 500;
            transition: all 0.3s ease;
            position: relative;
            padding-bottom: 4px;
        }

        .nav-center a::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 0;
            width: 0;
            height: 2px;
            background: linear-gradient(90deg, var(--primary-purple), #A855F7);
            transition: width 0.3s ease;
        }

        .nav-center a:hover {
            color: var(--primary-purple);
        }

        .nav-center a:hover::after {
            width: 100%;
        }

        .nav-center a.active {
            color: var(--primary-purple);
            font-weight: 600;
        }

        .nav-center a.active::after {
            width: 100%;
        }

        .nav-right {
            display: flex;
            align-items: center;
            gap: 24px;
        }

        .hamburger {
            display: none;
            flex-direction: column;
            cursor: pointer;
            gap: 5px;
        }

        .hamburger span {
            width: 24px;
            height: 2px;
            background: var(--gray-darker);
            border-radius: 2px;
            transition: all 0.3s ease;
        }

        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--primary-purple) 0%, #8B5CF6 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--white);
            font-weight: 700;
            font-size: 14px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 2px 8px rgba(109, 40, 217, 0.2);
        }

        .user-avatar:hover {
            transform: scale(1.12);
            box-shadow: var(--shadow-glow);
        }

        /* MAIN CONTAINER */
        .dashboard {
            max-width: 1200px;
            margin: 0 auto;
            padding: 48px 24px;
        }

        /* HERO SECTION */
        .hero-section {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 32px;
            margin-bottom: 80px;
        }

        .hero-card {
            background: linear-gradient(135deg, var(--white) 0%, #FAFBFF 100%);
            padding: 36px;
            border-radius: 24px;
            box-shadow: var(--shadow-md);
            transition: all 0.4s cubic-bezier(0.23, 1, 0.320, 1);
            cursor: pointer;
            border: 2px solid transparent;
            border-top: 3px solid var(--primary-purple-light);
            position: relative;
            overflow: hidden;
        }

        .hero-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, rgba(124, 58, 237, 0.05), transparent);
            opacity: 0;
            transition: opacity 0.4s ease;
            pointer-events: none;
        }

        .hero-card:hover {
            transform: translateY(-8px);
            box-shadow: var(--shadow-lg), var(--shadow-glow);
            border-color: var(--primary-purple-light);
            background: linear-gradient(135deg, var(--white) 0%, #F5F2FF 100%);
        }

        .hero-card:hover::before {
            opacity: 1;
        }

        .hero-card h3 {
            color: var(--primary-purple);
            font-size: 20px;
            margin-bottom: 12px;
            font-weight: 700;
        }

        .hero-card p {
            color: #6B7280;
            font-size: 14px;
            margin-bottom: 20px;
            line-height: 1.7;
        }

        .hero-card button {
            padding: 12px 32px;
            background: linear-gradient(135deg, var(--primary-purple-light) 0%, #7C3AED 100%);
            color: var(--white);
            border: none;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.23, 1, 0.320, 1);
            box-shadow: 0 4px 16px rgba(124, 58, 237, 0.3);
        }

        .hero-card button:hover {
            background: linear-gradient(135deg, var(--primary-purple) 0%, var(--primary-purple-light) 100%);
            transform: translateY(-2px);
            box-shadow: 0 8px 28px rgba(91, 33, 182, 0.4);
        }

        .hero-card button:active {
            transform: translateY(0);
        }

        /* PRODUCT SECTIONS */
        .product-section {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 56px;
            align-items: center;
            margin-bottom: 96px;
            padding: 0;
        }

        .product-text {
            display: flex;
            flex-direction: column;
        }

        .product-text h2 {
            color: var(--primary-purple);
            font-size: 36px;
            margin-bottom: 20px;
            font-weight: 800;
            letter-spacing: -0.7px;
            line-height: 1.2;
        }

        .product-text p {
            color: #4B5563;
            font-size: 16px;
            line-height: 1.9;
            margin-bottom: 32px;
        }

        .product-button {
            padding: 16px 44px;
            background: linear-gradient(135deg, var(--primary-purple-light) 0%, #7C3AED 100%);
            color: var(--white);
            border: none;
            border-radius: 12px;
            font-size: 15px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.23, 1, 0.320, 1);
            width: fit-content;
            box-shadow: 0 6px 20px rgba(124, 58, 237, 0.35);
        }

        .product-button:hover {
            background: linear-gradient(135deg, var(--primary-purple) 0%, var(--primary-purple-light) 100%);
            transform: translateY(-3px);
            box-shadow: 0 12px 36px rgba(91, 33, 182, 0.4);
        }

        .product-button:active {
            transform: translateY(0);
        }

        .product-image {
            background: linear-gradient(135deg, rgba(168, 85, 247, 0.08) 0%, rgba(217, 70, 239, 0.05) 100%);
            border-radius: 16px;
            padding: 48px;
            display: flex;
            align-items: center;
            justify-content: center;
            min-height: 400px;
            box-shadow: 0 4px 20px rgba(109, 40, 217, 0.1);
            border: 2px solid rgba(168, 85, 247, 0.15);
            color: var(--primary-purple);
            font-size: 64px;
            font-weight: 700;
            text-align: center;
            transition: all 0.3s ease;
        }

        .product-section:hover .product-image {
            box-shadow: 0 8px 30px rgba(109, 40, 217, 0.15);
            border-color: rgba(168, 85, 247, 0.25);
            background: linear-gradient(135deg, rgba(168, 85, 247, 0.12) 0%, rgba(217, 70, 239, 0.08) 100%);
        }

        /* PLAYBOOK CARD - Remove placeholder styling */
        .product-image.playbook-card {
            background: transparent;
            padding: 0;
            min-height: auto;
            box-shadow: none;
            border: none;
            display: block;
            overflow: visible;
        }

        .product-section:hover .product-image.playbook-card {
            box-shadow: none;
            border-color: transparent;
            background: transparent;
        }

        /* VALUE HIGHLIGHTS PANEL */
        .value-highlights-panel {
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.8) 0%, rgba(250, 248, 255, 0.6) 100%);
            border-radius: 20px;
            padding: 40px;
            border: 1.5px solid rgba(124, 58, 237, 0.25);
            backdrop-filter: blur(12px);
            transition: all 0.4s cubic-bezier(0.23, 1, 0.320, 1);
            box-shadow: var(--shadow-md);
        }

        .value-highlights-panel:hover {
            border-color: rgba(124, 58, 237, 0.4);
            box-shadow: var(--shadow-lg), var(--shadow-glow);
        }

        .highlights-header {
            margin-bottom: 28px;
        }

        .highlights-subtitle {
            font-size: 11px;
            font-weight: 800;
            color: var(--primary-purple-light);
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .highlights-grid {
            display: flex;
            flex-direction: column;
            gap: 18px;
        }

        .highlight-item {
            display: flex;
            gap: 14px;
            align-items: flex-start;
            transition: all 0.3s ease;
            padding: 14px;
            border-radius: 12px;
            cursor: default;
        }

        .highlight-item:hover {
            background: rgba(124, 58, 237, 0.08);
            padding-left: 18px;
        }

        .highlight-icon {
            font-size: 28px;
            flex-shrink: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            width: 44px;
            height: 44px;
            background: linear-gradient(135deg, rgba(124, 58, 237, 0.15), rgba(196, 181, 253, 0.1));
            border-radius: 12px;
            border: 1.5px solid rgba(124, 58, 237, 0.25);
        }

        .highlight-text {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 4px;
        }

        .highlight-title {
            font-size: 14px;
            font-weight: 700;
            color: var(--primary-purple);
            line-height: 1.3;
        }

        .highlight-desc {
            font-size: 12px;
            color: var(--primary-purple-light);
            line-height: 1.5;
            font-weight: 500;
        }

        .reverse {
            grid-template-columns: 1fr 1fr;
        }

        .reverse .product-text {
            order: 2;
        }

        .reverse .product-image {
            order: 1;
        }

        /* RESPONSIVE */
        @media (max-width: 768px) {
            header {
                padding: 16px 20px;
            }

            .nav-center {
                display: none;
            }

            .hamburger {
                display: flex;
            }

            .dashboard {
                padding: 32px 16px;
            }

            .hero-section {
                grid-template-columns: 1fr;
            }

            .hero-card {
                padding: 24px;
            }

            .product-section {
                grid-template-columns: 1fr;
                gap: 32px;
            }

            .reverse {
                grid-template-columns: 1fr;
            }

            .reverse .product-text,
            .reverse .product-image {
                order: unset;
            }

            .product-text h2 {
                font-size: 24px;
            }

            .product-image {
                min-height: 300px;
                padding: 32px;
            }

            .login-container {
                padding: 32px;
            }
        }

        @media (max-width: 480px) {
            .dashboard {
                padding: 20px 12px;
            }

            .hero-card {
                padding: 16px;
            }

            .hero-card h3 {
                font-size: 18px;
            }

            .product-text h2 {
                font-size: 20px;
            }

            .product-text p {
                font-size: 14px;
            }

            .hero-subcards {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <!-- LOGIN MODAL -->
    <div class="login-modal" id="loginModal">
        <div class="login-container">
            <div class="login-header">
                <h1>Welcome to Autonomy360</h1>
                <p>Intelligent Enterprise Automation Platform</p>
            </div>
            <form id="loginForm">
                <div class="form-group">
                    <label for="email">Email Address</label>
                    <input type="email" id="email" placeholder="you@example.com" required>
                </div>
                <div class="form-group">
                    <label for="password">Password</label>
                    <input type="password" id="password" placeholder="••••••••" required>
                    <div class="error-message" id="errorMessage">Please fill in all fields</div>
                </div>
                <button type="submit" class="login-button">Sign In</button>
            </form>
        </div>
    </div>

    <!-- HEADER -->
    <header>
        <div class="logo">
            <svg class="logo-svg" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
                <!-- Outer circle -->
                <circle cx="50" cy="50" r="45" fill="none" stroke="#6D28D9" stroke-width="2"/>
                
                <!-- Inner circle -->
                <circle cx="50" cy="50" r="30" fill="none" stroke="#C4B5FD" stroke-width="1.5" opacity="0.6"/>
                
                <!-- 360 degree arc indicators -->
                <path d="M 50 20 A 30 30 0 0 1 80 50" fill="none" stroke="#6D28D9" stroke-width="3" stroke-linecap="round"/>
                <path d="M 80 50 A 30 30 0 0 1 50 80" fill="none" stroke="#A855F7" stroke-width="3" stroke-linecap="round"/>
                <path d="M 50 80 A 30 30 0 0 1 20 50" fill="none" stroke="#C4B5FD" stroke-width="3" stroke-linecap="round" opacity="0.8"/>
                
                <!-- Center node -->
                <circle cx="50" cy="50" r="5" fill="#6D28D9"/>
                
                <!-- Network nodes -->
                <circle cx="50" cy="25" r="3" fill="#A855F7"/>
                <circle cx="70" cy="50" r="3" fill="#A855F7"/>
                <circle cx="50" cy="75" r="3" fill="#A855F7"/>
                <circle cx="30" cy="50" r="3" fill="#A855F7"/>
                
                <!-- Connection lines -->
                <line x1="50" y1="50" x2="50" y2="25" stroke="#C4B5FD" stroke-width="1.5" opacity="0.6"/>
                <line x1="50" y1="50" x2="70" y2="50" stroke="#C4B5FD" stroke-width="1.5" opacity="0.6"/>
                <line x1="50" y1="50" x2="50" y2="75" stroke="#C4B5FD" stroke-width="1.5" opacity="0.6"/>
                <line x1="50" y1="50" x2="30" y2="50" stroke="#C4B5FD" stroke-width="1.5" opacity="0.6"/>
            </svg>
            <span class="logo-text">Autonomy360</span>
        </div>
        <nav class="nav-center">
            <a href="#playbook" class="nav-item">Playbook</a>
            <a href="#toolkits" class="nav-item">Toolkits</a>
            <a href="#framework" class="nav-item">Framework</a>
        </nav>
        <div class="nav-right">
            <div class="hamburger" id="hamburger">
                <span></span>
                <span></span>
                <span></span>
            </div>
            <div class="user-avatar" title="User Profile">AS</div>
        </div>
    </header>

    <!-- DASHBOARD -->
    <div class="dashboard">
        <!-- HERO SECTION -->
        <section class="hero-section" id="features">
            <div class="hero-card">
                <h3>Active Engagement Panel</h3>
                <p>Resume your previous work and maintain productivity with instant access to recent AN initiatives and progress tracking.</p>
                <button onclick="launchActiveEngagementPanel()">Continue Work</button>
            </div>
            <div class="hero-card">
                <h3>Personalized AN Control Center</h3>
                <p>Organize and manage your autonomous network priorities in one unified decision-making dashboard for maximum impact.</p>
                <button onclick="launchPersonalizedControlCenter()">Open Control Center</button>
            </div>
        </section>

        <!-- AUTONOMOUS NETWORK PLAYBOOK SECTION -->
        <section class="product-section" id="products">
            <div class="product-text">
                <h2>Autonomous Network Playbook</h2>
                <p>Strategic guidance and operating models for building intelligent, self-healing networks. Comprehensive execution roadmap covering architecture, governance, and change management for seamless autonomous network transformation.</p>
                <button class="product-button" onclick="launchPlaybook()">Launch Playbook</button>
            </div>
            <div class="value-highlights-panel">
                <div class="highlights-header">
                    <div class="highlights-subtitle">What's included</div>
                </div>
                <div class="highlights-grid">
                    <div class="highlight-item">
                        <div class="highlight-icon">📋</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Strategic Guidance</div>
                            <div class="highlight-desc">Intelligent network models and best practices</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🏗️</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Target Operating Models</div>
                            <div class="highlight-desc">Governance and organizational design</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🤖</div>
                        <div class="highlight-text">
                            <div class="highlight-title">AI-Driven Roadmap</div>
                            <div class="highlight-desc">Execution timeline and implementation</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">⚡</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Change Enablement</div>
                            <div class="highlight-desc">Transformation and adoption strategies</div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- AN MATURITY ASSESSMENT TOOLKIT SECTION -->
        <section class="product-section reverse">
            <div class="value-highlights-panel">
                <div class="highlights-header">
                    <div class="highlights-subtitle">What you'll get</div>
                </div>
                <div class="highlights-grid">
                    <div class="highlight-item">
                        <div class="highlight-icon">📈</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Current State Scoring</div>
                            <div class="highlight-desc">Detailed maturity assessment across five dimensions</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🔍</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Gap Identification</div>
                            <div class="highlight-desc">Analysis of gaps across AN strategy, architecture, and operations</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">⭐</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Benchmark Comparison</div>
                            <div class="highlight-desc">Position against industry standards and target maturity levels</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🗺️</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Roadmap Recommendations</div>
                            <div class="highlight-desc">Prioritized initiatives and execution timeline for improvement</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="product-text">
                <h2>AN Maturity Assessment Toolkit</h2>
                <p>Evaluate your organization's current state across technology, people, and process dimensions. Identify gaps, benchmark against industry standards, and establish a clear roadmap for advancing autonomous network maturity and capability.</p>
                <button class="product-button" onclick="window.location.href='assessment-landing.html'">Launch Assessment</button>
                    <!-- AN MATURITY ASSESSMENT MODAL -->
                    <div id="assessmentModal" style="display:none;position:fixed;top:0;left:0;width:100vw;height:100vh;background:rgba(91,33,182,0.10);z-index:2000;align-items:center;justify-content:center;overflow:auto;">
                        <div style="background:#fff;max-width:650px;width:98vw;margin:40px auto;border-radius:18px;box-shadow:0 8px 32px rgba(91,33,182,0.18);padding:0 0 24px 0;position:relative;">
                            <div style="display:flex;align-items:center;justify-content:space-between;padding:24px 32px 0 32px;">
                                <h2 style="color:#6c4bb6;font-size:1.6rem;font-weight:700;">AN Maturity Assessment Toolkit</h2>
                                <button onclick="closeAssessmentModal()" style="background:none;border:none;font-size:2rem;color:#6c4bb6;cursor:pointer;">&times;</button>
                            </div>
                            <div id="assessmentApp" style="padding:0 32px;"></div>
                        </div>
                    </div>
                    <script>
                    // Modal open/close
                    function openAssessmentModal() {
                        document.getElementById('assessmentModal').style.display = 'flex';
                        startAssessmentApp();
                    }
                    function closeAssessmentModal() {
                        document.getElementById('assessmentModal').style.display = 'none';
                    }
                    // Assessment App Logic (minimal, loads JSON and renders one question at a time)
                    async function startAssessmentApp() {
                        const container = document.getElementById('assessmentApp');
                        container.innerHTML = '<div style="text-align:center;padding:32px;">Loading questions...</div>';
                        let questions = [];
                        try {
                            const res = await fetch('an_maturity_questions.json');
                            questions = await res.json();
                        } catch {
                            container.innerHTML = '<div style="color:#b71c1c;text-align:center;">Failed to load questions.</div>';
                            return;
                        }
                        let idx = 0;
                        let answers = JSON.parse(localStorage.getItem('an-maturity-modal-answers')||'{}');
                        function renderQ() {
                            const q = questions[idx];
                            if (!q) return;
                            container.innerHTML = `
                                <div style='margin:24px 0 0 0;'>
                                    <div style='color:#6c4bb6;font-weight:600;font-size:1.1rem;margin-bottom:8px;'>${q.domain} / ${q.subDomain} &mdash; <span>${q.questionId}</span></div>
                                    <div style='font-size:1.15rem;color:#4b3570;font-weight:500;margin-bottom:10px;'>${q.question}</div>
                                    <span style='display:inline-block;background:#b39ddb;color:#fff;border-radius:8px;padding:4px 12px;font-size:0.98rem;font-weight:500;margin-bottom:18px;'>${q.activity}</span>
                                    <form id='optionsForm' style='margin:18px 0 0 0;'>
                                        ${q.options.map((opt,i) =>
                                            `<label style='display:block;background:#f3eaff;border-radius:8px;padding:12px 18px;margin-bottom:10px;cursor:pointer;color:#6c4bb6;font-weight:500;'><input type='radio' name='option' value='${opt.key}' ${answers[q.questionId]===opt.key?'checked':''}> ${opt.text}</label>`
                                        ).join('')}
                                    </form>
                                    <div style='display:flex;gap:16px;justify-content:center;margin:24px 0;'>
                                        <button onclick='prevQ()' class='product-button' style='background:#ede7f6;color:#6c4bb6;border:1px solid #6c4bb6;' ${idx===0?'disabled':''}>Previous</button>
                                        <button onclick='nextQ()' class='product-button' style='background:#6c4bb6;color:#fff;' ${!answers[q.questionId]?'disabled':''}>${idx===questions.length-1?'Submit':'Next'}</button>
                                    </div>
                                    <div style='text-align:center;color:#6c4bb6;font-size:1rem;font-weight:500;'>Question ${idx+1} of ${questions.length}</div>
                                </div>
                            `;
                            document.getElementById('optionsForm').addEventListener('change', e => {
                                if (e.target.name === 'option') {
                                    answers[q.questionId] = e.target.value;
                                    localStorage.setItem('an-maturity-modal-answers', JSON.stringify(answers));
                                    renderQ();
                                }
                            });
                        }
                        window.prevQ = function() { if (idx>0) { idx--; renderQ(); } };
                        window.nextQ = function() {
                            if (idx < questions.length-1) { idx++; renderQ(); }
                            else renderResults();
                        };
                        function renderResults() {
                            // Scoring
                            let totalWeighted = 0, totalWeight = 0, domainScores = {};
                            questions.forEach(q => {
                                const selectedKey = answers[q.questionId];
                                const opt = q.options.find(o => o.key === selectedKey);
                                if (opt) {
                                    const weighted = opt.score * q.weight;
                                    totalWeighted += weighted;
                                    totalWeight += q.weight;
                                    if (!domainScores[q.domain]) domainScores[q.domain] = {weighted:0, weight:0};
                                    domainScores[q.domain].weighted += weighted;
                                    domainScores[q.domain].weight += q.weight;
                                }
                            });
                            const normalized = totalWeight ? (totalWeighted/totalWeight) : 0;
                            let band = '';
                            if (normalized < 1) band = 'Initial';
                            else if (normalized < 2) band = 'Developing';
                            else if (normalized < 3) band = 'Defined';
                            else if (normalized < 3.5) band = 'Advanced';
                            else band = 'Autonomous';
                            let table = `<table style='width:100%;margin:18px 0;border-collapse:collapse;'><tr><th style='background:#ede7f6;color:#6c4bb6;padding:8px;'>Domain</th><th style='background:#ede7f6;color:#6c4bb6;padding:8px;'>Score</th><th style='background:#ede7f6;color:#6c4bb6;padding:8px;'>Band</th></tr>`;
                            Object.keys(domainScores).forEach(domain => {
                                const score = domainScores[domain].weight ? (domainScores[domain].weighted/domainScores[domain].weight) : 0;
                                let dband = '';
                                if (score < 1) dband = 'Initial';
                                else if (score < 2) dband = 'Developing';
                                else if (score < 3) dband = 'Defined';
                                else if (score < 3.5) dband = 'Advanced';
                                else dband = 'Autonomous';
                                table += `<tr><td style='padding:8px;'>${domain}</td><td style='padding:8px;'>${score.toFixed(2)}</td><td style='padding:8px;'>${dband}</td></tr>`;
                            });
                            table += '</table>';
                            container.innerHTML = `
                                <div style='margin:32px 0 0 0;text-align:center;'>
                                    <div style='font-size:1.3rem;color:#4b3570;font-weight:700;margin-bottom:10px;'>Overall Score: ${normalized.toFixed(2)}</div>
                                    <div style='font-size:1.1rem;color:#6c4bb6;font-weight:600;margin-bottom:18px;'>Maturity Band: ${band}</div>
                                    ${table}
                                    <button onclick='closeAssessmentModal()' class='product-button' style='margin-top:18px;'>Close</button>
                                </div>
                            `;
                        }
                        renderQ();
                    }
                    </script>
            </div>
        </section>

        <!-- AI READINESS TOOLKIT SECTION -->
        <section class="product-section">
            <div class="product-text">
                <h2>AI Readiness Toolkit for AN</h2>
                <p>Assess your organization's readiness for AI-driven autonomous networks across data infrastructure, platform capabilities, and technical skills. Identify enablers and barriers to unlock intelligent, self-optimizing network operations.</p>
                <button class="product-button" onclick="launchAIReadinessToolkit()">Launch Toolkit</button>
            </div>
            <div class="value-highlights-panel">
                <div class="highlights-header">
                    <div class="highlights-subtitle">What you'll discover</div>
                </div>
                <div class="highlights-grid">
                    <div class="highlight-item">
                        <div class="highlight-icon">📊</div>
                        <div class="highlight-text">
                            <div class="highlight-title">AI Data and Platform Readiness</div>
                            <div class="highlight-desc">Visibility into your data and technology stack preparedness</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🔍</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Gap Analysis Across Capabilities</div>
                            <div class="highlight-desc">Comprehensive assessment of readiness gaps and enablers</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">⚙️</div>
                        <div class="highlight-text">
                            <div class="highlight-title">MLOps and Governance Preparedness</div>
                            <div class="highlight-desc">Evaluation of operating models and management capabilities</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🚀</div>
                        <div class="highlight-text">
                            <div class="highlight-title">AI-Enabled AN Roadmap</div>
                            <div class="highlight-desc">Prioritized actions for AI adoption and autonomous networks</div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- VALUE REALIZATION FRAMEWORK SECTION -->
        <section class="product-section reverse">
            <div class="value-highlights-panel">
                <div class="highlights-header">
                    <div class="highlights-subtitle">Key benefits</div>
                </div>
                <div class="highlights-grid">
                    <div class="highlight-item">
                        <div class="highlight-icon">🔗</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Business Value Linkage</div>
                            <div class="highlight-desc">Clear connection between AN initiatives and measurable business value</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">📊</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Structured Benefits Tracking</div>
                            <div class="highlight-desc">Governance processes and KPIs for transparent value measurement</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">✓</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Value Assurance</div>
                            <div class="highlight-desc">Risk management and value leakage mitigation strategies</div>
                        </div>
                    </div>
                    <div class="highlight-item">
                        <div class="highlight-icon">🚀</div>
                        <div class="highlight-text">
                            <div class="highlight-title">Continuous Optimization</div>
                            <div class="highlight-desc">Ongoing improvements to maximize value realization over time</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="product-text">
                <h2>Value Realization Framework for AN</h2>
                <p>Establish comprehensive benefit tracking and value measurement strategies for autonomous networks. Define KPIs, quantify business impact, and ensure stakeholder alignment throughout your transformation journey.</p>
                <button class="product-button" onclick="launchValueRealizationFramework()">Launch Framework</button>
            </div>
        </section>
    </div>

    <script>
        // ==========================================
        // SESSION & AUTHENTICATION MANAGEMENT
        // ==========================================
        
        const loginForm = document.getElementById('loginForm');
        const loginModal = document.getElementById('loginModal');
        const errorMessage = document.getElementById('errorMessage');
        const emailInput = document.getElementById('email');
        const passwordInput = document.getElementById('password');
        const userAvatar = document.querySelector('.user-avatar');

        // Check if user is already authenticated on page load
        function checkAuthentication() {
            const isAuthenticated = sessionStorage.getItem('isAuthenticated') === 'true';
            const userEmail = sessionStorage.getItem('userEmail');
            
            if (isAuthenticated && userEmail) {
                // User is already logged in, hide login modal
                loginModal.classList.add('hidden');
                updateUserAvatar(userEmail);
            } else {
                // User is not logged in, show login modal
                loginModal.classList.remove('hidden');
            }
        }

        // Update user avatar with initials
        function updateUserAvatar(email) {
            const initials = email
                .split('@')[0]
                .split('.')
                .map(part => part.charAt(0).toUpperCase())
                .join('')
                .substring(0, 2);
            userAvatar.textContent = initials;
            userAvatar.title = `Logged in as ${email}`;
        }

        // Handle login form submission
        loginForm.addEventListener('submit', function(e) {
            e.preventDefault();

            // Validation
            if (!emailInput.value.trim() || !passwordInput.value.trim()) {
                errorMessage.classList.add('show');
                return;
            }

            if (!emailInput.value.includes('@')) {
                errorMessage.textContent = 'Please enter a valid email address';
                errorMessage.classList.add('show');
                return;
            }

            // Store authentication in session storage
            sessionStorage.setItem('isAuthenticated', 'true');
            sessionStorage.setItem('userEmail', emailInput.value);

            // Hide modal and show dashboard
            loginModal.classList.add('hidden');
            errorMessage.classList.remove('show');

            // Update avatar
            updateUserAvatar(emailInput.value);

            // Show welcome message
            alert(`✓ Welcome!\n\nSuccessfully logged in as ${emailInput.value}\n\nYou can now access all Autonomy360 features.`);

            // Clear form
            loginForm.reset();
        });

        // Clear error on input
        emailInput.addEventListener('input', () => {
            errorMessage.classList.remove('show');
        });

        passwordInput.addEventListener('input', () => {
            errorMessage.classList.remove('show');
        });

        // Handle logout
        function logout() {
            if (confirm('Are you sure you want to logout?')) {
                // Clear session storage
                sessionStorage.removeItem('isAuthenticated');
                sessionStorage.removeItem('userEmail');
                
                // Show login modal
                loginModal.classList.remove('hidden');
                
                // Reset form
                loginForm.reset();
                errorMessage.classList.remove('show');

                // Reset avatar
                userAvatar.textContent = 'AS';
                userAvatar.title = 'User Profile';

                alert('✓ Logged out successfully\n\nYou have been logged out of your session.');
            }
        }

        // Check authentication on page load
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', checkAuthentication);
        } else {
            checkAuthentication();
        }

        // BUTTON CLICK HANDLERS
        function handleButtonClick(buttonName) {
            alert(`✓ ${buttonName} button clicked!\n\nThis feature is ready to launch.`);
        }

        function launchPlaybook() {
            window.location.href = 'playbook.html';
        }

        function launchMaturityAssessment() {
            window.location.href = 'an-maturity-assessment.html';
        }

        function launchAIReadinessToolkit() {
            window.location.href = 'ai-readiness-toolkit.html';
        }

        function launchValueRealizationFramework() {
            window.location.href = 'value-realization-framework.html';
        }

        function launchActiveEngagementPanel() {
            window.location.href = 'active-engagement-panel.html';
        }

        function launchPersonalizedControlCenter() {
            window.location.href = 'personalized-control-center.html';
        }

        function handleCardClick(cardName) {
            alert(`✓ ${cardName} card clicked!\n\nExploring ${cardName} section...`);
        }

        // NAVIGATION SCROLL AND ACTIVE STATE
        const navLinks = document.querySelectorAll('.nav-center a');
        const sections = [
            { id: 'playbook', selector: '.hero-section' },
            { id: 'toolkits', selector: '.product-section:nth-of-type(1)' },
            { id: 'framework', selector: '.product-section:nth-of-type(4)' }
        ];

        navLinks.forEach(link => {
            link.addEventListener('click', function(e) {
                e.preventDefault();
                const targetId = this.getAttribute('href').substring(1);
                const targetSection = sections.find(s => s.id === targetId);
                
                if (targetSection) {
                    const element = document.querySelector(targetSection.selector);
                    if (element) {
                        element.scrollIntoView({ behavior: 'smooth' });
                    }
                }

                // Update active state
                navLinks.forEach(l => l.classList.remove('active'));
                this.classList.add('active');
            });
        });

        // Update active nav link on scroll
        window.addEventListener('scroll', function() {
            let current = '';
            sections.forEach((section, index) => {
                const element = document.querySelector(section.selector);
                if (element) {
                    const rect = element.getBoundingClientRect();
                    if (rect.top <= 150) {
                        current = section.id;
                    }
                }
            });

            navLinks.forEach(link => {
                link.classList.remove('active');
                if (link.getAttribute('href').substring(1) === current) {
                    link.classList.add('active');
                }
            });
        });

        // HAMBURGER MENU
        const hamburger = document.getElementById('hamburger');
        hamburger.addEventListener('click', function() {
            alert('☰ Menu opened\n\nNavigation menu would expand here on mobile devices.');
        });

        // USER AVATAR CLICK - Profile Menu with Logout
        userAvatar.addEventListener('click', function() {
            const isAuthenticated = sessionStorage.getItem('isAuthenticated') === 'true';
            const userEmail = sessionStorage.getItem('userEmail');
            
            if (isAuthenticated && userEmail) {
                const action = confirm(`👤 Profile Menu\n\nLogged in as: ${userEmail}\n\nClick OK to Logout`);
                if (action) {
                    logout();
                }
            } else {
                alert('👤 Please login first to access profile options.');
            }
        });
    </script>
</body>
</html>
