<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="BIMZ — The #1 AI-powered BIM & CAD platform for MEP engineers.">
    <title>BIMZ — Smart BIM & CAD Platform</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;500;600;700&family=Exo+2:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg:#020408; --bg2:#060d18; --bg3:#0a1525; --card:#0d1b2e; --card2:#112036;
            --cyan:#00d4ff; --cyan2:#22e5ff; --blue:#0057ff; --blue2:#2979ff; --border:rgba(0,212,255,0.12);
            --border2:rgba(0,212,255,0.3); --green:#00e5a0; --amber:#ffb800; --red:#ff4d6a;
            --text:#dce8f0; --text2:#7ea8c0; --text3:#445a6e; --glow:0 0 40px rgba(0,212,255,0.2);
        }
        *,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
        html{scroll-behavior:smooth}
        body{font-family:"Exo 2",sans-serif;background:var(--bg);color:var(--text);overflow-x:hidden;line-height:1.6}
        ::-webkit-scrollbar{width:3px}
        ::-webkit-scrollbar-thumb{background:linear-gradient(var(--blue),var(--cyan));border-radius:2px}
        #progress-bar{position:fixed;top:0;left:0;height:2px;width:0%;background:linear-gradient(90deg,var(--blue),var(--cyan));z-index:9999;transition:width .1s}
        #bg-canvas{position:fixed;top:0;left:0;width:100%;height:100%;z-index:0;pointer-events:none}
        .grid-bg{position:fixed;inset:0;z-index:0;pointer-events:none; background-image:linear-gradient(rgba(0,212,255,0.03) 1px,transparent 1px), linear-gradient(90deg,rgba(0,212,255,0.03) 1px,transparent 1px); background-size:60px 60px}
        nav{position:fixed;top:0;left:0;right:0;z-index:1000;display:flex;align-items:center;justify-content:space-between; padding:0 2rem;height:70px;background:rgba(2,4,8,0.88);backdrop-filter:blur(24px); border-bottom:1px solid var(--border);transition:all .3s}
        nav.scrolled{background:rgba(2,4,8,0.98);border-color:var(--border2);box-shadow:0 1px 40px rgba(0,212,255,0.1)}
        .nav-logo{display:flex;align-items:center;text-decoration:none}
        .nav-logo img{height:38px;width:auto;filter:drop-shadow(0 0 10px rgba(0,212,255,0.4))}
        .nav-links{display:flex;gap:2px}
        .nav-links a{padding:7px 15px;color:var(--text2);text-decoration:none;font-size:.85rem;border-radius:6px; transition:all .2s;font-weight:600;letter-spacing:.05em;text-transform:uppercase;font-size:.78rem}
        .nav-links a:hover{color:var(--cyan);background:rgba(0,212,255,0.07)}
        .nav-links a.active-link{color:var(--cyan)}
        .nav-right{display:flex;align-items:center;gap:10px}
        .nav-search{display:flex;align-items:center;gap:8px;padding:7px 14px;background:var(--card); border:1px solid var(--border);border-radius:8px;color:var(--text3);font-size:.8rem;cursor:pointer;transition:all .2s}
        .nav-search:hover{border-color:var(--cyan);color:var(--text2)}
        .nav-search kbd{background:var(--bg3);border:1px solid var(--border);border-radius:4px;padding:1px 5px; font-family:"JetBrains Mono",monospace;font-size:.7rem}
        .btn-login{padding:7px 18px;border:1px solid var(--border2);border-radius:8px;color:var(--cyan); font-size:.82rem;font-weight:700;cursor:pointer;background:transparent;font-family:"Exo 2",sans-serif; transition:all .2s;letter-spacing:.05em}
        .btn-login:hover{background:rgba(0,212,255,0.1);color:#fff;box-shadow:0 0 15px rgba(0,212,255,0.2)}
        .hamburger{display:none;flex-direction:column;gap:5px;cursor:pointer;padding:4px}
        .hamburger span{width:22px;height:2px;background:var(--text2);border-radius:1px;transition:all .3s}
        .mobile-nav{display:none;position:fixed;top:70px;left:0;right:0;z-index:999; background:rgba(2,4,8,0.98);backdrop-filter:blur(24px);border-bottom:1px solid var(--border); padding:1rem;flex-direction:column;gap:4px}
        .mobile-nav.open{display:flex}
        .mobile-nav a{padding:13px 16px;color:var(--text2);text-decoration:none;border-radius:8px;font-weight:600; transition:all .2s;text-transform:uppercase;letter-spacing:.05em;font-size:.85rem}
        .mobile-nav a:hover{background:var(--card);color:var(--cyan)}
        .cmd-overlay{display:none;position:fixed;inset:0;z-index:2000;background:rgba(0,0,0,0.75);backdrop-filter:blur(6px)}
        .cmd-overlay.open{display:flex;align-items:flex-start;justify-content:center;padding-top:13vh}
        .cmd-box{width:90%;max-width:600px;background:var(--bg2);border:1px solid var(--border2);border-radius:16px; overflow:hidden;box-shadow:0 0 100px rgba(0,212,255,0.2),0 20px 60px rgba(0,0,0,0.7)}
        .cmd-input-wrap{display:flex;align-items:center;gap:12px;padding:18px 22px;border-bottom:1px solid var(--border)}
        .cmd-input{flex:1;background:none;border:none;outline:none;color:#fff;font-size:1rem;font-family:"Exo 2",sans-serif}
        .cmd-results{max-height:320px;overflow-y:auto}
        .cmd-item{display:flex;align-items:center;gap:14px;padding:13px 22px;cursor:pointer; transition:background .15s;border-bottom:1px solid rgba(255,255,255,0.03)}
        .cmd-item:hover,.cmd-item.active{background:rgba(0,212,255,0.08)}
        .cmd-item-icon{font-size:1.15rem;width:28px;text-align:center}
        .cmd-item-title{font-size:.9rem;font-weight:600;color:var(--text)}
        .cmd-item-sub{font-size:.75rem;color:var(--text3)}
        .cmd-footer{padding:10px 22px;border-top:1px solid var(--border);display:flex;gap:18px; color:var(--text3);font-size:.73rem;font-family:"JetBrains Mono",monospace}
        .cmd-footer kbd{background:var(--bg3);border:1px solid var(--border);border-radius:4px;padding:1px 6px}
        .page{display:none;min-height:100vh;padding-top:70px;position:relative;z-index:1}
        .page.active{display:block}
        .section-pad{padding:90px 2rem}
        .container{max-width:1200px;margin:0 auto}
        .section-label{font-family:"JetBrains Mono",monospace;font-size:.72rem;color:var(--cyan); letter-spacing:.18em;text-transform:uppercase;margin-bottom:10px}
        .section-title{font-family:"Rajdhani",sans-serif;font-size:clamp(2.2rem,4.5vw,3.5rem);font-weight:700; letter-spacing:-.01em;line-height:1.05;margin-bottom:1rem}
        .grad-text{background:linear-gradient(120deg,var(--cyan),var(--blue2)); -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
        .divider{width:50px;height:2px;background:linear-gradient(90deg,var(--cyan),var(--blue)); margin-bottom:1.8rem;border-radius:1px}
        .section-sub{color:var(--text2);font-size:1rem;max-width:540px;line-height:1.8;margin-bottom:3rem}
        .reveal{opacity:0;transform:translateY(28px);transition:opacity .7s ease,transform .7s ease}
        .reveal.visible{opacity:1;transform:none}
        @keyframes fadeUp{from{opacity:0;transform:translateY(20px)}to{opacity:1;transform:none}}
        .hero{min-height:calc(100vh - 70px);display:flex;flex-direction:column;align-items:center; justify-content:center;padding:80px 2rem 60px;text-align:center}
        .hero-badge{display:inline-flex;align-items:center;gap:9px;background:rgba(0,212,255,0.07); border:1px solid rgba(0,212,255,0.25);border-radius:100px;padding:7px 18px;font-size:.78rem; color:var(--cyan2);font-family:"JetBrains Mono",monospace;margin-bottom:2.5rem; animation:fadeUp .6s ease both}
        .hero-badge::before{content:"";width:7px;height:7px;border-radius:50%;background:var(--cyan); box-shadow:0 0 10px var(--cyan);animation:pulse 2s infinite}
        @keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.5;transform:scale(.8)}}
        .hero h1{font-family:"Rajdhani",sans-serif;font-size:clamp(3.5rem,9vw,7.5rem);font-weight:700; line-height:.95;margin-bottom:1.8rem;letter-spacing:-.02em;animation:fadeUp .6s ease .1s both}
        .hero-sub{max-width:580px;color:var(--text2);font-size:1.05rem;margin-bottom:3rem;line-height:1.8; animation:fadeUp .6s ease .2s both}
        .hero-ctas{display:flex;gap:14px;flex-wrap:wrap;justify-content:center;margin-bottom:3.5rem; animation:fadeUp .6s ease .3s both}
        .btn-primary{padding:13px 30px;background:linear-gradient(135deg,var(--blue),var(--cyan)); border:none;border-radius:10px;color:#fff;font-size:.95rem;font-weight:700;cursor:pointer; font-family:"Exo 2",sans-serif;transition:all .2s;box-shadow:0 0 30px rgba(0,87,255,0.4); display:flex;align-items:center;gap:9px;letter-spacing:.04em}
        .btn-primary:hover{transform:translateY(-2px);box-shadow:0 0 50px rgba(0,212,255,0.45)}
        .btn-outline{padding:13px 30px;background:transparent;border:1px solid var(--border2);border-radius:10px; color:var(--text);font-size:.95rem;font-weight:700;cursor:pointer;font-family:"Exo 2",sans-serif; transition:all .2s;display:flex;align-items:center;gap:9px;letter-spacing:.04em}
        .btn-outline:hover{background:rgba(0,212,255,0.08);border-color:var(--cyan);color:var(--cyan)}
        .stats-bar{display:flex;gap:3rem;animation:fadeUp .6s ease .4s both}
        .stat{text-align:center}
        .stat-num{font-family:"Rajdhani",sans-serif;font-size:2.2rem;font-weight:700; background:linear-gradient(135deg,var(--cyan),var(--blue2)); -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
        .stat-label{font-size:.78rem;color:var(--text3);letter-spacing:.1em;text-transform:uppercase}
        .stats-sep{width:1px;background:var(--border)}
        .platform-overview{width:100%;max-width:960px;margin:3.5rem auto 0;animation:fadeUp .8s ease .5s both}
        .plat-cards{display:flex;gap:10px;flex-wrap:wrap;justify-content:center}
        .plat-card{background:var(--card);border:1px solid var(--border);border-radius:10px; padding:12px 18px;display:flex;align-items:center;gap:10px;font-size:.84rem;font-weight:600; transition:all .3s;cursor:pointer;letter-spacing:.02em}
        .plat-card:hover{border-color:var(--cyan);box-shadow:var(--glow);transform:translateY(-3px)}
        .plat-badge{font-size:.62rem;font-family:"JetBrains Mono",monospace;padding:2px 7px; border-radius:4px;font-weight:600}
        .badge-free{background:rgba(0,229,160,0.12);color:var(--green);border:1px solid rgba(0,229,160,0.3)}
        .badge-new{background:rgba(0,87,255,0.15);color:var(--blue2);border:1px solid rgba(0,87,255,0.35)}
        .badge-ai{background:rgba(0,212,255,0.12);color:var(--cyan);border:1px solid rgba(0,212,255,0.3)}
        .badge-pack{background:rgba(255,184,0,0.12);color:var(--amber);border:1px solid rgba(255,184,0,0.3)}
        .filter-tabs{display:flex;gap:7px;flex-wrap:wrap;margin-bottom:2.5rem}
        .filter-tab{padding:7px 17px;border-radius:8px;border:1px solid var(--border);background:transparent; color:var(--text2);font-size:.8rem;font-weight:600;cursor:pointer;font-family:"Exo 2",sans-serif; transition:all .2s;letter-spacing:.04em}
        .filter-tab:hover{border-color:var(--cyan);color:var(--cyan)}
        .filter-tab.active{background:linear-gradient(135deg,var(--blue),var(--cyan));border-color:transparent; color:#fff;box-shadow:0 0 20px rgba(0,212,255,0.3)}
        .tools-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(360px,1fr));gap:20px}
        .tool-card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:24px; transition:all .3s;position:relative;overflow:hidden;cursor:pointer}
        .tool-card::before{content:"";position:absolute;inset:0; background:linear-gradient(135deg,rgba(0,212,255,0.04),transparent);opacity:0;transition:opacity .3s}
        .tool-card:hover{border-color:var(--border2);box-shadow:var(--glow);transform:translateY(-5px)}
        .tool-card:hover::before{opacity:1}
        .tool-card-header{display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:16px}
        .tool-icon{font-size:2rem}
        .tool-badges{display:flex;gap:6px;flex-wrap:wrap}
        .tool-title{font-family:"Rajdhani",sans-serif;font-size:1.15rem;font-weight:700;margin-bottom:7px;letter-spacing:.02em}
        .tool-desc{font-size:.84rem;color:var(--text2);line-height:1.7;margin-bottom:16px}
        .tool-code{background:var(--bg);border:1px solid var(--border);border-radius:8px;padding:12px; margin-bottom:16px;font-family:"JetBrains Mono",monospace;font-size:.72rem;color:var(--cyan2); overflow-x:auto;white-space:pre;line-height:1.6}
        .tool-meta{display:flex;align-items:center;justify-content:space-between; padding-top:15px;border-top:1px solid var(--border)}
        .tool-rating{display:flex;align-items:center;gap:6px;font-size:.82rem}
        .stars{color:var(--amber);letter-spacing:-1px}
        .tool-dl{font-size:.78rem;color:var(--text3);font-family:"JetBrains Mono",monospace}
        .tool-actions{display:flex;gap:8px}
        .btn-dl{padding:7px 14px;background:rgba(0,212,255,0.1);border:1px solid var(--border2); border-radius:8px;color:var(--cyan);font-size:.8rem;font-weight:600;cursor:pointer; font-family:"Exo 2",sans-serif;transition:all .2s}
        .btn-dl:hover{background:linear-gradient(135deg,var(--blue),var(--cyan));color:#fff; border-color:transparent;box-shadow:0 0 15px rgba(0,212,255,0.3)}
        .btn-save{padding:7px 10px;background:transparent;border:1px solid var(--border);border-radius:8px; color:var(--text3);font-size:.9rem;cursor:pointer;transition:all .2s}
        .btn-save:hover{border-color:var(--red);color:var(--red)}
        .btn-save.saved{color:var(--red);border-color:var(--red)}
        .compare-table{width:100%;border-collapse:collapse;margin-top:2rem}
        .compare-table th{padding:15px 20px;text-align:left;font-size:.84rem;background:var(--bg3); border:1px solid var(--border);font-weight:700;font-family:"Rajdhani",sans-serif;letter-spacing:.05em}
        .compare-table th:nth-child(2){color:var(--amber)}
        .compare-table th:nth-child(3){color:var(--cyan)}
        .compare-table td{padding:13px 20px;border:1px solid var(--border);font-size:.84rem; background:var(--card);transition:background .2s}
        .compare-table tr:hover td{background:var(--card2)}
        .compare-table td:first-child{color:var(--text2);font-weight:600}
        .chk{color:var(--green)}
        .crs{color:var(--red)}
        .hlf{color:var(--amber)}
        .ai-layout{display:grid;grid-template-columns:1fr 1fr;gap:24px}
        .bot-card{grid-column:1/-1;background:var(--card);border:1px solid rgba(0,212,255,0.25); border-radius:20px;overflow:hidden;box-shadow:0 0 60px rgba(0,212,255,0.08)}
        .bot-header{padding:20px 26px;background:linear-gradient(135deg,rgba(0,87,255,0.12),rgba(0,212,255,0.06)); border-bottom:1px solid rgba(0,212,255,0.15);display:flex;align-items:center;justify-content:space-between}
        .bot-title-wrap{display:flex;align-items:center;gap:14px}
        .bot-avatar{width:44px;height:44px;background:linear-gradient(135deg,var(--blue),var(--cyan)); border-radius:13px;display:flex;align-items:center;justify-content:center;font-size:1.2rem; box-shadow:0 0 20px rgba(0,212,255,0.4)}
        .bot-name{font-family:"Rajdhani",sans-serif;font-weight:700;font-size:1.1rem;letter-spacing:.04em}
        .bot-status{font-size:.73rem;color:var(--green);display:flex;align-items:center;gap:5px;margin-top:2px}
        .bot-status::before{content:"";width:6px;height:6px;border-radius:50%;background:var(--green); box-shadow:0 0 7px var(--green);animation:pulse 2s infinite}
        .bot-tags{display:flex;gap:8px;flex-wrap:wrap}
        .bot-tag{padding:4px 11px;border-radius:6px;font-size:.7rem;font-family:"JetBrains Mono",monospace; font-weight:600;background:rgba(0,212,255,0.1);color:var(--cyan);border:1px solid rgba(0,212,255,0.25)}
        .chat-suggestions{padding:0 26px 12px;display:flex;gap:8px;flex-wrap:wrap}
        .suggestion{padding:5px 13px;border:1px solid var(--border);border-radius:20px;font-size:.74rem; color:var(--text3);cursor:pointer;transition:all .2s}
        .suggestion:hover{border-color:var(--cyan);color:var(--cyan)}
        .chat-window{height:380px;overflow-y:auto;padding:20px 26px;display:flex;flex-direction:column; gap:16px;scroll-behavior:smooth}
        .chat-window::-webkit-scrollbar{width:3px}
        .chat-window::-webkit-scrollbar-thumb{background:var(--border2)}
        .msg{display:flex;gap:10px;max-width:88%}
        .msg.user{align-self:flex-end;flex-direction:row-reverse}
        .msg-av{width:33px;height:33px;border-radius:10px;flex-shrink:0;display:flex;align-items:center; justify-content:center;font-size:.85rem}
        .msg.bot .msg-av{background:linear-gradient(135deg,var(--blue),var(--cyan))}
        .msg.user .msg-av{background:var(--bg3);border:1px solid var(--border)}
        .msg-bubble{padding:12px 16px;border-radius:14px;font-size:.88rem;line-height:1.65}
        .msg.bot .msg-bubble{background:rgba(0,212,255,0.07);border:1px solid rgba(0,212,255,0.15); border-bottom-left-radius:4px}
        .msg.user .msg-bubble{background:rgba(255,255,255,0.05);border:1px solid rgba(255,255,255,0.1); border-bottom-right-radius:4px}
        .msg-bubble code{font-family:"JetBrains Mono",monospace;font-size:.79rem; background:rgba(0,212,255,0.1);color:var(--cyan2);padding:1px 5px;border-radius:4px}
        .msg-bubble pre{margin-top:10px;background:var(--bg);border:1px solid var(--border);border-radius:8px; padding:12px;font-family:"JetBrains Mono",monospace;font-size:.77rem;color:var(--cyan2); overflow-x:auto;white-space:pre-wrap;line-height:1.5}
        .typing{display:flex;gap:4px;align-items:center}
        .typing span{width:6px;height:6px;border-radius:50%;background:var(--cyan);animation:blink 1.2s infinite}
        .typing span:nth-child(2){animation-delay:.2s}
        .typing span:nth-child(3){animation-delay:.4s}
        @keyframes blink{0%,80%,100%{opacity:.2;transform:scale(.8)}40%{opacity:1;transform:scale(1)}}
        .chat-input-area{padding:16px 26px;border-top:1px solid var(--border);display:flex;gap:10px;align-items:flex-end}
        .chat-textarea{flex:1;background:var(--bg3);border:1px solid var(--border);border-radius:12px;color:#fff; font-size:.88rem;font-family:"Exo 2",sans-serif;padding:12px 15px;resize:none;outline:none; min-height:48px;max-height:120px;transition:border .2s;line-height:1.5}
        .chat-textarea:focus{border-color:var(--cyan)}
        .chat-send{width:48px;height:48px;border-radius:12px;flex-shrink:0; background:linear-gradient(135deg,var(--blue),var(--cyan));border:none;cursor:pointer;color:#fff; font-size:1.1rem;transition:all .2s;display:flex;align-items:center;justify-content:center; box-shadow:0 0 20px rgba(0,212,255,0.3)}
        .chat-send:hover{transform:scale(1.08);box-shadow:0 0 35px rgba(0,212,255,0.5)}
        .chat-send:disabled{opacity:.4;cursor:not-allowed;transform:none}
        .ai-feat-card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:24px; transition:all .3s;position:relative}
        .ai-feat-card:hover{border-color:var(--border2);box-shadow:var(--glow);transform:translateY(-3px)}
        .ai-feat-icon{font-size:1.9rem;margin-bottom:14px}
        .ai-feat-title{font-family:"Rajdhani",sans-serif;font-size:1.1rem;font-weight:700;margin-bottom:8px; letter-spacing:.03em}
        .ai-feat-desc{font-size:.84rem;color:var(--text2);line-height:1.7}
        .cs-badge{position:absolute;top:16px;right:16px;padding:3px 10px;background:rgba(68,90,110,0.2); border:1px solid var(--border);border-radius:6px;font-size:.68rem;color:var(--text3); font-family:"JetBrains Mono",monospace}
        .courses-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(340px,1fr));gap:20px}
        .course-card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:24px; transition:all .3s;display:flex;flex-direction:column;gap:12px;cursor:pointer}
        .course-card:hover{border-color:var(--border2);box-shadow:var(--glow);transform:translateY(-4px)}
        .course-top{display:flex;align-items:center;gap:13px}
        .course-icon{width:50px;height:50px;border-radius:13px;font-size:1.4rem;display:flex; align-items:center;justify-content:center;flex-shrink:0}
        .lvl-b{background:rgba(0,229,160,0.1);border:1px solid rgba(0,229,160,0.25)}
        .lvl-i{background:rgba(255,184,0,0.1);border:1px solid rgba(255,184,0,0.25)}
        .lvl-a{background:rgba(255,77,106,0.1);border:1px solid rgba(255,77,106,0.25)}
        .course-level{font-size:.68rem;font-family:"JetBrains Mono",monospace;font-weight:600;margin-bottom:3px}
        .level-b{color:var(--green)}
        .level-i{color:var(--amber)}
        .level-a{color:var(--red)}
        .course-title{font-family:"Rajdhani",sans-serif;font-size:1.05rem;font-weight:700;letter-spacing:.02em}
        .course-desc{font-size:.84rem;color:var(--text2);line-height:1.7;flex:1}
        .course-meta{display:flex;align-items:center;justify-content:space-between; padding-top:12px;border-top:1px solid var(--border)}
        .course-info{font-size:.76rem;color:var(--text3);font-family:"JetBrains Mono",monospace}
        .btn-start{padding:7px 15px;background:rgba(0,212,255,0.1);border:1px solid var(--border2); border-radius:8px;color:var(--cyan);font-size:.8rem;font-weight:600;cursor:pointer; font-family:"Exo 2",sans-serif;transition:all .2s}
        .btn-start:hover{background:linear-gradient(135deg,var(--blue),var(--cyan));color:#fff;border-color:transparent}
        .roadmap-tl{position:relative;padding-left:4px}
        .roadmap-tl::before{content:"";position:absolute;left:27px;top:0;bottom:0;width:2px; background:linear-gradient(180deg,var(--cyan),var(--blue),rgba(68,90,110,0.3));border-radius:1px}
        .phase{display:flex;gap:24px;margin-bottom:36px;position:relative}
        .phase-dot{width:56px;height:56px;border-radius:50%;flex-shrink:0;display:flex;align-items:center; justify-content:center;font-family:"Rajdhani",sans-serif;font-size:1.1rem;font-weight:700; border:2px solid;position:relative;z-index:1;background:var(--bg)}
        .p-done .phase-dot{border-color:var(--green);color:var(--green);box-shadow:0 0 20px rgba(0,229,160,0.25)}
        .p-active .phase-dot{border-color:var(--cyan);color:var(--cyan); box-shadow:0 0 25px rgba(0,212,255,0.4);animation:pulseBorder 2s infinite}
        .p-planned .phase-dot{border-color:var(--border);color:var(--text3)}
        @keyframes pulseBorder{0%,100%{box-shadow:0 0 25px rgba(0,212,255,0.4)}50%{box-shadow:0 0 45px rgba(0,212,255,0.7)}}
        .phase-content{background:var(--card);border:1px solid var(--border);border-radius:14px; padding:20px 24px;flex:1;transition:all .3s}
        .p-done .phase-content{border-color:rgba(0,229,160,0.18)}
        .p-active .phase-content{border-color:rgba(0,212,255,0.28);box-shadow:0 0 30px rgba(0,212,255,0.07)}
        .phase-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:8px}
        .phase-title{font-family:"Rajdhani",sans-serif;font-size:1.05rem;font-weight:700;letter-spacing:.03em}
        .p-status{font-size:.7rem;font-family:"JetBrains Mono",monospace;padding:3px 10px;border-radius:6px;font-weight:600}
        .s-done{background:rgba(0,229,160,0.12);color:var(--green);border:1px solid rgba(0,229,160,0.3)}
        .s-active{background:rgba(0,212,255,0.12);color:var(--cyan);border:1px solid rgba(0,212,255,0.3)}
        .s-planned{background:rgba(68,90,110,0.12);color:var(--text3);border:1px solid var(--border)}
        .phase-sub{font-size:.84rem;color:var(--text2);line-height:1.7}
        .phase-date{font-size:.74rem;color:var(--text3);font-family:"JetBrains Mono",monospace;margin-top:5px}
        .contact-grid{display:grid;grid-template-columns:1fr 1.6fr;gap:32px}
        .contact-cards{display:flex;flex-direction:column;gap:13px}
        .contact-card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:18px 20px; display:flex;align-items:center;gap:14px;text-decoration:none;transition:all .25s}
        .contact-card:hover{border-color:var(--border2);box-shadow:var(--glow);transform:translateX(5px)}
        .c-icon{width:46px;height:46px;border-radius:12px;flex-shrink:0;display:flex;align-items:center; justify-content:center;font-size:1.2rem}
        .ic-email{background:rgba(0,87,255,0.12);border:1px solid rgba(0,87,255,0.3)}
        .ic-wa{background:rgba(0,229,160,0.12);border:1px solid rgba(0,229,160,0.3)}
        .ic-co{background:rgba(255,184,0,0.12);border:1px solid rgba(255,184,0,0.3)}
        .ic-sp{background:rgba(0,212,255,0.08);border:1px solid var(--border)}
        .ic-web{background:rgba(41,121,255,0.12);border:1px solid rgba(41,121,255,0.3)}
        .c-title{font-size:.72rem;color:var(--text3);font-family:"JetBrains Mono",monospace;margin-bottom:2px}
        .c-val{font-size:.9rem;font-weight:600}
        .contact-form{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:28px}
        .form-row{display:grid;grid-template-columns:1fr 1fr;gap:14px;margin-bottom:14px}
        .form-group{display:flex;flex-direction:column;gap:6px;margin-bottom:14px}
        .form-label{font-size:.78rem;color:var(--text2);font-weight:600;letter-spacing:.04em}
        .form-input,.form-select,.form-textarea{background:var(--bg3);border:1px solid var(--border); border-radius:10px;color:#fff;font-size:.88rem;font-family:"Exo 2",sans-serif; padding:11px 14px;outline:none;transition:border .2s;width:100%}
        .form-input:focus,.form-select:focus,.form-textarea:focus{border-color:var(--cyan)}
        .form-select{appearance:none;cursor:pointer}
        .form-textarea{resize:vertical;min-height:110px;line-height:1.5}
        .btn-submit{width:100%;padding:14px;background:linear-gradient(135deg,var(--blue),var(--cyan)); border:none;border-radius:10px;color:#fff;font-size:.95rem;font-weight:700;cursor:pointer; font-family:"Exo 2",sans-serif;transition:all .2s;box-shadow:0 0 25px rgba(0,212,255,0.25); letter-spacing:.06em}
        .btn-submit:hover{box-shadow:0 0 45px rgba(0,212,255,0.45);transform:translateY(-1px)}
        footer{border-top:1px solid var(--border);padding:60px 2rem 30px;position:relative;z-index:1}
        .footer-grid{display:grid;grid-template-columns:2fr 1fr 1fr 1fr;gap:40px;margin-bottom:40px}
        .footer-brand p{font-size:.83rem;color:var(--text2);line-height:1.8;max-width:270px;margin-top:12px}
        .footer-logo{height:36px;width:auto;filter:drop-shadow(0 0 8px rgba(0,212,255,0.3))}
        .footer-col h4{font-size:.82rem;font-weight:700;margin-bottom:16px;color:var(--text); letter-spacing:.08em;text-transform:uppercase}
        .footer-col a{display:block;font-size:.82rem;color:var(--text3);text-decoration:none;margin-bottom:8px;transition:color .2s}
        .footer-col a:hover{color:var(--cyan)}
        .footer-bottom{border-top:1px solid var(--border);padding-top:24px;display:flex; align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px}
        .footer-copy{font-size:.78rem;color:var(--text3);font-family:"JetBrains Mono",monospace}
        .footer-links{display:flex;gap:20px}
        .footer-links a{font-size:.78rem;color:var(--text3);text-decoration:none;transition:color .2s}
        .footer-links a:hover{color:var(--text2)}
        .toast{position:fixed;bottom:28px;right:28px;z-index:5000;background:var(--card2); border:1px solid var(--green);border-radius:12px;padding:13px 20px;display:flex; align-items:center;gap:10px;box-shadow:0 0 30px rgba(0,229,160,0.25); transform:translateY(100px);opacity:0;transition:all .4s cubic-bezier(.34,1.56,.64,1); font-size:.88rem;font-weight:600}
        .toast.show{transform:translateY(0);opacity:1}
        #back-top{position:fixed;bottom:28px;left:28px;z-index:3000;width:44px;height:44px; border-radius:12px;background:var(--card);border:1px solid var(--border);color:var(--text2); font-size:1rem;cursor:pointer;display:flex;align-items:center;justify-content:center; opacity:0;pointer-events:none;transition:all .3s;box-shadow:0 4px 20px rgba(0,0,0,0.4)}
        #back-top.visible{opacity:1;pointer-events:all}
        #back-top:hover{border-color:var(--cyan);color:var(--cyan);transform:translateY(-3px)}
        @media(max-width:900px){ .nav-links{display:none} .hamburger{display:flex} .ai-layout{grid-template-columns:1fr} .contact-grid{grid-template-columns:1fr} .footer-grid{grid-template-columns:1fr 1fr} .tools-grid,.courses-grid{grid-template-columns:1fr} .form-row{grid-template-columns:1fr} .bot-tags{display:none} .stats-bar{gap:1.5rem} }
        @media(max-width:600px){ .hero h1{font-size:3rem} .stats-bar{flex-direction:column;gap:.8rem} .stats-sep{display:none} .footer-grid{grid-template-columns:1fr} .section-pad{padding:65px 1.2rem} nav{padding:0 1.2rem} }
    </style>
</head>
<body>
<div id="progress-bar"></div>
<canvas id="bg-canvas"></canvas>
<div class="grid-bg"></div>
<nav id="navbar">
    <a href="#" class="nav-logo" onclick="showPage('home');return false">
        <svg width="38" height="38" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M50 10 L85 30 L85 70 L50 90 L15 70 L15 30 L50 10Z" stroke="#00d4ff" stroke-width="2" fill="none"/>
            <path d="M50 25 L70 37 L70 63 L50 75 L30 63 L30 37 L50 25Z" stroke="#0057ff" stroke-width="2" fill="none" stroke-dasharray="4 2"/>
            <text x="50" y="55" text-anchor="middle" fill="#00d4ff" font-size="24" font-weight="bold" font-family="Rajdhani">B</text>
        </svg>
    </a>
    <div class="nav-links">
        <a href="#" onclick="showPage('tools');return false" data-page="tools">Tools</a>
        <a href="#" onclick="showPage('ai');return false" data-page="ai">AI</a>
        <a href="#" onclick="showPage('learning');return false" data-page="learning">Learn</a>
        <a href="#" onclick="showPage('roadmap');return false" data-page="roadmap">Roadmap</a>
        <a href="#" onclick="showPage('contact');return false" data-page="contact">Contact</a>
    </div>
    <div class="nav-right">
        <div class="nav-search" onclick="openCmd()">
            <svg width="14" height="14" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/></svg> Search...&nbsp;<kbd>⌘K</kbd>
        </div>
        <button class="btn-login">Login</button>
        <div class="hamburger" onclick="toggleMobile()"><span></span><span></span><span></span></div>
    </div>
</nav>
<div class="mobile-nav" id="mobile-nav">
    <a href="#" onclick="showPage('tools');closeMobile();return false">Tools</a>
    <a href="#" onclick="showPage('ai');closeMobile();return false">AI</a>
    <a href="#" onclick="showPage('learning');closeMobile();return false">Learn</a>
    <a href="#" onclick="showPage('roadmap');closeMobile();return false">Roadmap</a>
    <a href="#" onclick="showPage('contact');closeMobile();return false">Contact</a>
</div>
<div class="cmd-overlay" id="cmd-overlay" onclick="closeCmd(event)">
    <div class="cmd-box">
        <div class="cmd-input-wrap">
            <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.35-4.35"/></svg>
            <input class="cmd-input" id="cmd-input" placeholder="Search tools, pages, sections..." oninput="filterCmd(this.value)" onkeydown="cmdKey(event)">
        </div>
        <div class="cmd-results" id="cmd-results"></div>
        <div class="cmd-footer">
            <span><kbd>↵</kbd> select</span><span><kbd>↑↓</kbd> navigate</span><span><kbd>esc</kbd> close</span>
        </div>
    </div>
</div>
<div class="page active" id="page-home">
    <section class="hero">
        <div class="hero-badge">AI-Powered BIM Platform &nbsp;·&nbsp; Free Forever</div>
        <h1><span class="grad-text">BIMZ</span><br>Platform</h1>
        <p class="hero-sub">The #1 smart platform for BIM &amp; CAD engineers. Free LISP scripts, Dynamo workflows, Revit families, AI assistant, and structured learning — all in one place.</p>
        <div class="hero-ctas">
            <button class="btn-primary" onclick="showPage('tools')">⚡ Explore Tools</button>
            <button class="btn-outline" onclick="showPage('ai')">🤖 BIMZ Bot</button>
        </div>
        <div class="stats-bar">
            <div class="stat"><div class="stat-num">80+</div><div class="stat-label">Tools</div></div>
            <div class="stats-sep"></div>
            <div class="stat"><div class="stat-num">12k</div><div class="stat-label">Downloads</div></div>
            <div class="stats-sep"></div>
            <div class="stat"><div class="stat-num">100%</div><div class="stat-label">Free</div></div>
            <div class="stats-sep"></div>
            <div class="stat"><div class="stat-num">6</div><div class="stat-label">Disciplines</div></div>
        </div>
        <div class="platform-overview">
            <div class="plat-cards">
                <div class="plat-card" onclick="showPage('lisp')">⚡ LISP Scripts <span class="plat-badge badge-free">50+ FREE</span></div>
                <div class="plat-card" onclick="showPage('dynamo')">🔷 Dynamo Nodes <span class="plat-badge badge-free">30+ FREE</span></div>
                <div class="plat-card" onclick="showPage('revit')">🏠 Revit Families <span class="plat-badge badge-new">NEW</span></div>
                <div class="plat-card" onclick="showPage('ai')">🤖 BIMZ Bot <span class="plat-badge badge-ai">AI LIVE</span></div>
                <div class="plat-card" onclick="showPage('tools')">🔤 Fonts + Styles <span class="plat-badge badge-pack">PACK</span></div>
                <div class="plat-card" onclick="showPage('tools')">🌿 Enscape Tools <span class="plat-badge badge-ai">AI</span></div>
            </div>
        </div>
    </section>
</div>
<div class="page" id="page-tools">
    <div class="section-pad">
        <div class="container">
            <div class="reveal">
                <div class="section-label">Tools Library</div>
                <h2 class="section-title">Professional BIM &amp;<br><span class="grad-text">CAD Tools</span></h2>
                <div class="divider"></div>
                <p class="section-sub">Download tested scripts, nodes, and resources — rated and reviewed by real engineers on real projects.</p>
            </div>
            <div class="filter-tabs reveal">
                <button class="filter-tab active" onclick="filterTools('all',this)">All</button>
                <button class="filter-tab" onclick="filterTools('autocad',this)">AutoCAD</button>
                <button class="filter-tab" onclick="filterTools('revit',this)">Revit</button>
                <button class="filter-tab" onclick="filterTools('dynamo',this)">Dynamo</button>
                <button class="filter-tab" onclick="filterTools('mep',this)">MEP</button>
                <button class="filter-tab" onclick="filterTools('arch',this)">Architecture</button>
                <button class="filter-tab" onclick="filterTools('ai',this)">AI Tools</button>
            </div>
            <div class="tools-grid" id="tools-grid">
                <div class="tool-card reveal" data-cat="autocad mep" onclick="#">
                    <div class="tool-card-header">
                        <div class="tool-icon">⚡</div>
                        <div class="tool-badges"><span class="plat-badge badge-free">Free</span><span class="plat-badge" style="background:rgba(255,77,106,.12);color:#ff8fa3;border:1px solid rgba(255,77,106,.3)">🔥 Popular</span></div>
                    </div>
                    <div class="tool-title">AutoCAD LISP Tools</div>
                    <div class="tool-desc">50+ battle-tested routines — layer management, block insertion, dimension tools, MEP annotations, and full automation scripts for production drawings.</div>
                    <div class="tool-code">;; Script by ZIAD ANTER - 2025 ;; MEP Layer Setup Automation (defun c:BZ-Layer () (command "LAYER" "M" "MEP-HVAC" "C" "5" "" "LW" "0.35" "" "") )</div>
                    <div class="tool-meta">
                        <div class="tool-rating"><span class="stars">★★★★★</span><span style="color:var(--text2)">4.9</span><span style="color:var(--text3)">(312)</span></div>
                        <div class="tool-dl">↓ 8.2k</div>
                        <div class="tool-actions">
                            <button class="btn-dl" onclick="event.stopPropagation();showToast('Downloading LISP Pack...')">Download ↓</button>
                            <button class="btn-save" onclick="event.stopPropagation();toggleSave(this)">♡</button>
                        </div>
                    </div>
                </div>
                <div class="tool-card reveal" data-cat="dynamo revit mep" onclick="#">
                    <div class="tool-card-header">
                        <div class="tool-icon">🔷</div>
                        <div class="tool-badges"><span class="plat-badge badge-free">Free</span><span class="plat-badge badge-new">New</span></div>
                    </div>
                    <div class="tool-title">Dynamo Scripts</div>
                    <div class="tool-desc">Visual programming for Revit — automate families, parameters, MEP schedules, pipe tagging, duct routing, and clash workflows. No coding required.</div>
                    <div class="tool-code">// Pipe Tag Automation — BIMZ // Auto-tag all untagged pipes in view // System type + Diameter + Elevation</div>
                    <div class="tool-meta">
                        <div class="tool-rating"><span class="stars">★★★★½</span><span style="color:var(--text2)">4.7</span><span style="color:var(--text3)">(187)</span></div>
                        <div class="tool-dl">↓ 4.1k</div>
                        <div class="tool-actions">
                            <button class="btn-dl" onclick="event.stopPropagation();showToast('Downloading Dynamo Pack...')">Download ↓</button>
                            <button class="btn-save" onclick="event.stopPropagation();toggleSave(this)">♡</button>
                        </div>
                    </div>
                </div>
                <div class="tool-card reveal" data-cat="revit mep arch" onclick="#">
                    <div class="tool-card-header">
                        <div class="tool-icon">🏠</div>
                        <div class="tool-badges"><span class="plat-badge badge-free">Free</span></div>
                    </div>
                    <div class="tool-title">Revit Families</div>
                    <div class="tool-desc">Parametric MEP families — HVAC diffusers, pipe fittings, valve schedules, pumps, equipment, and fire suppression components ready for shop drawings.</div>
                    <div class="tool-meta" style="padding-top:0">
                        <div class="tool-rating"><span class="stars">★★★★★</span><span style="color:var(--text2)">4.8</span><span style="color:var(--text3)">(241)</span></div>
                        <div class="tool-dl">↓ 3.3k</div>
                        <div class="tool-actions">
                            <button class="btn-dl" onclick="event.stopPropagation();showToast('Downloading Revit Families...')">Download ↓</button>
                            <button class="btn-save" onclick="event.stopPropagation();toggleSave(this)">♡</button>
                        </div>
                    </div>
                </div>
                <div class="tool-card reveal" data-cat="autocad" onclick="#">
                    <div class="tool-card-header">
                        <div class="tool-icon">🔤</div>
                        <div class="tool-badges"><span class="plat-badge badge-free">Free</span></div>
                    </div>
                    <div class="tool-title">AutoCAD Fonts</div>
                    <div class="tool-desc">Complete SHX + TTF font pack — Egyptian, ISOCPEUR, Romans, Complex, and 20+ standard engineering fonts in one download.</div>
                    <div class="tool-meta" style="padding-top:0">
                        <div class="tool-rating"><span class="stars">★★★★</span><span style="color:var(--text2)">4.5</span><span style="color:var(--text3)">(156)</span></div>
                        <div class="tool-dl">↓ 6.8k</div>
                        <div class="tool-actions">
                            <button class="btn-dl" onclick="event.stopPropagation();showToast('Downloading Font Pack...')">Download ↓</button>
                            <button class="btn-save" onclick="event.stopPropagation();toggleSave(this)">♡</button>
                        </div>
                    </div>
                </div>
                <div class="tool-card reveal" data-cat="autocad arch" onclick="#">
                    <div class="tool-card-header">
                        <div class="tool-icon">🎨</div>
                        <div class="tool-badges"><span class="plat-badge badge-free">Free</span></div>
                    </div>
                    <div class="tool-title">Plot Styles (CTB/STB)</div>
                    <div class="tool-desc">Professional CTB and STB files. Standardized line weights and colors — ISO, DIN, and custom MEP standards for perfect engineering output.</div>
                    <div class="tool-meta" style="padding-top:0">
                        <div class="tool-rating"><span class="stars">★★★★½</span><span style="color:var(--text2)">4.6</span><span style="color:var(--text3)">(203)</span></div>
                        <div class="tool-dl">↓ 5.1k</div>
                        <div class="tool-actions">
                            <button class="btn-dl" onclick="event.stopPropagation();showToast('Downloading Plot Styles...')">Download ↓</button>
                            <button class="btn-save" onclick="event.stopPropagation();toggleSave(this)">♡</button>
                        </div>
                    </div>
                </div>
                <div class="tool-card reveal" data-cat="revit ai arch" onclick="#">
                    <div class="tool-card-header">
                        <div class="tool-icon">🌿</div>
                        <div class="tool-badges"><span class="plat-badge badge-new">New</span><span class="plat-badge badge-ai">AI</span></div>
                    </div>
                    <div class="tool-title">Enscape Tools</div>
                    <div class="tool-desc">Render presets, material packs, and AI-enhanced settings for MEP and architectural visualization. Professional renders from Revit in minutes.</div>
                    <div class="tool-meta" style="padding-top:0">
                        <div class="tool-rating"><span class="stars">★★★★</span><span style="color:var(--text2)">4.4</span><span style="color:var(--text3)">(98)</span></div>
                        <div class="tool-dl">↓ 2.7k</div>
                        <div class="tool-actions">
                            <button class="btn-dl" onclick="event.stopPropagation();showToast('Downloading Enscape Pack...')">Download ↓</button>
                            <button class="btn-save" onclick="event.stopPropagation();toggleSave(this)">♡</button>
                        </div>
                    </div>
                </div>
            </div>
            <div style="margin-top:80px" class="reveal">
                <div class="section-label">Decision Guide</div>
                <h2 class="section-title">LISP vs Dynamo —<br><span class="grad-text">When to Use What</span></h2>
                <div class="divider"></div>
                <div style="overflow-x:auto">
                    <table class="compare-table">
                        <thead>
                            <tr><th>Criteria</th><th>⚡ AutoCAD LISP</th><th>🔷 Dynamo</th></tr>
                        </thead>
                        <tbody>
                            <tr><td>Software</td><td>AutoCAD</td><td>Revit / AutoCAD</td></tr>
                            <tr><td>Learning Curve</td><td>Medium</td><td>Low (Visual)</td></tr>
                            <tr><td>BIM Integration</td><td><span class="crs">✗</span> None</td><td><span class="chk">✓</span> Full</td></tr>
                            <tr><td>2D Drawing Automation</td><td><span class="chk">✓</span> Excellent</td><td><span class="hlf">◐</span> Limited</td></tr>
                            <tr><td>Parameter Control</td><td><span class="hlf">◐</span> Basic</td><td><span class="chk">✓</span> Full</td></tr>
                            <tr><td>MEP Scheduling</td><td><span class="crs">✗</span> Manual</td><td><span class="chk">✓</span> Automated</td></tr>
                            <tr><td>Execution Speed</td><td><span class="chk">✓</span> Very Fast</td><td><span class="hlf">◐</span> Medium</td></tr>
                            <tr><td>Clash Detection</td><td><span class="crs">✗</span> No</td><td><span class="chk">✓</span> Yes</td></tr>
                            <tr><td>Shop Drawings</td><td><span class="chk">✓</span> Best</td><td><span class="hlf">◐</span> Possible</td></tr>
                            <tr><td>Best For</td><td>AutoCAD-heavy workflows</td><td>BIM-first projects</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>
<div class="page" id="page-lisp">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <button class="btn-outline" style="margin-bottom:2rem;padding:8px 16px;font-size:.82rem" onclick="showPage('tools')">← Back to Tools</button>
            <div class="section-label">AutoCAD Tools</div>
            <h2 class="section-title">⚡ LISP Scripts<br><span class="grad-text">50+ Free Routines</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Battle-tested AutoCAD LISP routines for MEP engineers. Layer management, block insertion, dimension tools, MEP annotations, and full automation. All scripts include author header and documentation.</p>
        </div>
        <div class="tools-grid reveal">
            <div class="tool-card"><div class="tool-title">BZ-Layer — MEP Layer Setup</div><div class="tool-desc">Creates and sets MEP layers with correct color, lineweight, and linetype in one command.</div><div class="tool-code">;; Script by ZIAD ANTER - 2025 (defun c:BZ-Layer () (command "LAYER" "M" "MEP-HVAC" "C" "5" "" "LW" "0.35" "" "")) </div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★★</span> 4.9</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">BZ-TextRot — Rotate All Text</div><div class="tool-desc">Rotates all TEXT and MTEXT entities to match current UCS angle. Useful for drawing cleanup.</div><div class="tool-code">;; Script by ZIAD ANTER - 2025 (defun c:BZ-TextRot ( / ss i en ed) (setq ss (ssget "X" '((0 . "TEXT")))) (setq i 0) (while (< i (sslength ss)) (setq en (ssname ss i) ed (entget en) ed (subst (cons 50 0.0) (assoc 50 ed) ed)) (entmod ed) (setq i (1+ i)))) </div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★★</span> 4.8</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">BZ-FindReplace — Find & Replace</div><div class="tool-desc">Advanced find & replace in text entities across the entire drawing with DCL dialog interface.</div><div class="tool-code">;; Script by ZIAD ANTER - 2025 ;; Portable VLX — no external files (defun c:BZ-FR () ;; DCL loaded inline (alert "Script by ZIAD ANTER - 2025")) </div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★★</span> 5.0</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">BZ-BlockCount — Count Blocks</div><div class="tool-desc">Counts all block insertions in the drawing by name and exports a summary report to the command line or a text file.</div><div class="tool-code">;; Script by ZIAD ANTER - 2025 (defun c:BZ-Count ( / ss n) (setq ss (ssget "X" '((0 . "INSERT")))) (alert (strcat "Total blocks: " (itoa (sslength ss))))) </div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★</span> 4.6</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
        </div>
    </div></div>
</div>
<div class="page" id="page-dynamo">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <button class="btn-outline" style="margin-bottom:2rem;padding:8px 16px;font-size:.82rem" onclick="showPage('tools')">← Back to Tools</button>
            <div class="section-label">Revit Automation</div>
            <h2 class="section-title">🔷 Dynamo Scripts<br><span class="grad-text">MEP Workflows</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Visual programming for Revit — automate families, parameters, MEP schedules, pipe tagging, duct routing, and clash workflows without writing a single line of code.</p>
        </div>
        <div class="tools-grid reveal">
            <div class="tool-card"><div class="tool-title">Pipe Auto-Tagger</div><div class="tool-desc">Tags all untagged pipes in the active view with system type, diameter, and elevation. Works on all pipe categories simultaneously.</div><div class="tool-code">// BIMZ Dynamo — Pipe Auto Tagger // Nodes: All Elements of Category // → Filter Untagged → Tag By Element // Output: Tags placed on all pipes // Author: ZIAD ANTER 2025</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★★</span> 4.9</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">MEP Schedule Exporter</div><div class="tool-desc">Extracts HVAC, Plumbing, and Firefighting schedules from Revit and exports them to Excel with custom formatting.</div><div class="tool-code">// BIMZ Dynamo — Schedule Exporter // Nodes: Schedule → DataTable // → Excel.WriteToFile // Supports: Pipes, Ducts, Fittings // Author: ZIAD ANTER 2025</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★½</span> 4.7</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">Duct Size Validator</div><div class="tool-desc">Checks all duct sizes in the model against the HVAC sizing schedule and flags undersized or oversized ducts in red.</div><div class="tool-code">// BIMZ Dynamo — Duct Validator // Compares duct sizes vs schedule // Flags mismatches with warning // Outputs: List of non-compliant ducts // Author: ZIAD ANTER 2025</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★</span> 4.5</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">Sheet Auto-Numberer</div><div class="tool-desc">Automatically numbers all sheets in the Revit project according to a discipline prefix system (M-, P-, F-, E-).</div><div class="tool-code">// BIMZ Dynamo — Sheet Numberer // Prefix: M=Mech P=Plumb F=Fire // Auto-increments per discipline // Renames all sheets in sequence // Author: ZIAD ANTER 2025</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★</span> 4.4</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
        </div>
    </div></div>
</div>
<div class="page" id="page-revit">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <button class="btn-outline" style="margin-bottom:2rem;padding:8px 16px;font-size:.82rem" onclick="showPage('tools')">← Back to Tools</button>
            <div class="section-label">Revit Content</div>
            <h2 class="section-title">🏠 Revit Families<br><span class="grad-text">MEP Parametric Content</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Parametric MEP families ready for shop drawings. HVAC diffusers, pipe fittings, valve schedules, pumps, equipment, and fire suppression components.</p>
        </div>
        <div class="tools-grid reveal">
            <div class="tool-card"><div class="tool-title">HVAC Diffusers Pack</div><div class="tool-desc">Square, round, and linear diffusers with adjustable neck size, face size, and airflow parameters. Fully schedulable.</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★★</span> 4.9</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">Pipe Fittings Library</div><div class="tool-desc">Elbows, tees, reducers, and flanges for all pipe categories — parametric diameters, materials, and pressure ratings.</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★½</span> 4.7</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">Valve Schedule Family</div><div class="tool-desc">Gate, globe, butterfly, and check valves with full parameter sets for scheduling, tagging, and coordination.</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★★</span> 4.8</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
            <div class="tool-card"><div class="tool-title">Fire Suppression Pack</div><div class="tool-desc">Sprinkler heads, fire hose cabinets, and suppression system components parametric for shop drawing production.</div><div class="tool-meta" style="padding-top:0"><div class="tool-rating"><span class="stars">★★★★</span> 4.6</div><button class="btn-dl" onclick="showToast('Downloading...')">Download ↓</button></div></div>
        </div>
    </div></div>
</div>
<div class="page" id="page-ai">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <div class="section-label">Smart Features</div>
            <h2 class="section-title">AI &amp; Smart<br><span class="grad-text">Engineering Tools</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Powered by large language models specialized in BIM, LISP, and Dynamo. Ask anything — get engineer-grade answers instantly.</p>
        </div>
        <div class="ai-layout">
            <div class="bot-card reveal">
                <div class="bot-header">
                    <div class="bot-title-wrap">
                        <div class="bot-avatar">🤖</div>
                        <div><div class="bot-name">BIMZ Bot — AI Assistant</div><div class="bot-status">Online &nbsp;·&nbsp; Powered by Claude AI</div></div>
                    </div>
                    <div class="bot-tags"><span class="bot-tag">LISP Help</span><span class="bot-tag">Dynamo Debug</span><span class="bot-tag">MEP Q&A</span><span class="bot-tag">Revit</span><span class="bot-tag">Shop Drawings</span></div>
                </div>
                <div class="chat-suggestions">
                    <span class="suggestion" onclick="useSuggestion(this)">How do I tag all pipes in Revit with Dynamo?</span>
                    <span class="suggestion" onclick="useSuggestion(this)">Write a LISP to rotate all text to 0°</span>
                    <span class="suggestion" onclick="useSuggestion(this)">HVAC duct sizing formula?</span>
                    <span class="suggestion" onclick="useSuggestion(this)">How to solve pipe clash in Navisworks?</span>
                    <span class="suggestion" onclick="useSuggestion(this)">Revit shared parameters explained</span>
                    <span class="suggestion" onclick="useSuggestion(this)">اكتبلي LISP يعمل layer للميكانيكا</span>
                </div>
                <div class="chat-window" id="chat-window">
                    <div class="msg bot"><div class="msg-av">🤖</div><div class="msg-bubble">مرحباً! أنا <strong>BIMZ Bot</strong> — مساعدك الذكي لكل أسئلة BIM والـ CAD.<br><br>اسألني عن: AutoCAD LISP · Dynamo · Revit · MEP Systems · Navisworks · Shop Drawings.<br><br>Engineer-grade answers only. 🔧</div></div>
                </div>
                <div class="chat-input-area">
                    <textarea class="chat-textarea" id="chat-input" placeholder="Ask BIMZ Bot anything about BIM, LISP, Dynamo, MEP..." rows="1" onkeydown="chatKeydown(event)" oninput="autoResize(this)"></textarea>
                    <button class="chat-send" id="send-btn" onclick="sendMessage()">✈️</button>
                </div>
            </div>
            <div class="ai-feat-card reveal"><div class="cs-badge">Coming Soon</div><div class="ai-feat-icon">⚡</div><div class="ai-feat-title">LISP Generator</div><div class="ai-feat-desc">Describe what you need in plain English — BIMZ generates ready-to-use LISP code with comments, error handling, and DCL dialogs.</div></div>
            <div class="ai-feat-card reveal"><div class="cs-badge">Coming Soon</div><div class="ai-feat-icon">🔷</div><div class="ai-feat-title">Dynamo Optimizer</div><div class="ai-feat-desc">Paste your Dynamo script — AI analyzes it, finds bottlenecks, and suggests optimized node sequences with better performance.</div></div>
            <div class="ai-feat-card reveal"><div class="cs-badge">Coming Soon</div><div class="ai-feat-icon">🔍</div><div class="ai-feat-title">Problem → Solution Engine</div><div class="ai-feat-desc">Describe your site issue or clash — the engine matches known solutions and generates a structured resolution report.</div></div>
            <div class="ai-feat-card reveal"><div class="cs-badge">Coming Soon</div><div class="ai-feat-icon">📁</div><div class="ai-feat-title">File Analyzer</div><div class="ai-feat-desc">Upload CAD or Revit files — AI checks for standard compliance, naming issues, missing parameters, and coordination problems.</div></div>
        </div>
    </div></div>
</div>
<div class="page" id="page-learning">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <div class="section-label">Learning Center</div>
            <h2 class="section-title">Courses &amp;<br><span class="grad-text">Learning Paths</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Structured content for every level — beginner to advanced BIM automation. Built by engineers, for engineers.</p>
        </div>
        <div class="courses-grid">
            <div class="course-card reveal"><div class="course-top"><div class="course-icon lvl-b">⚡</div><div><div class="course-level level-b">Beginner</div><div class="course-title">LISP Scripting Fundamentals</div></div></div><div class="course-desc">Start from zero — syntax, basic commands, and your first AutoCAD automation routine. No prior programming experience needed.</div><div class="course-meta"><div class="course-info">12 lessons · 3.5 hrs</div><button class="btn-start" onclick="showToast('Course coming soon!')">Start →</button></div></div>
            <div class="course-card reveal"><div class="course-top"><div class="course-icon lvl-i">🔷</div><div><div class="course-level level-i">Intermediate</div><div class="course-title">Dynamo for MEP Engineers</div></div></div><div class="course-desc">Practical Dynamo workflows — HVAC duct routing, piping automation, equipment scheduling, and pipe tagging without manual effort.</div><div class="course-meta"><div class="course-info">18 lessons · 5 hrs</div><button class="btn-start" onclick="showToast('Course coming soon!')">Start →</button></div></div>
            <div class="course-card reveal"><div class="course-top"><div class="course-icon lvl-a">🏗️</div><div><div class="course-level level-a">Advanced</div><div class="course-title">Revit BIM Coordination</div></div></div><div class="course-desc">Shop drawings, clash detection, and multi-discipline coordination using Navisworks + Revit. Real project workflows from start to delivery.</div><div class="course-meta"><div class="course-info">22 lessons · 7 hrs</div><button class="btn-start" onclick="showToast('Course coming soon!')">Start →</button></div></div>
            <div class="course-card reveal"><div class="course-top"><div class="course-icon lvl-b">🌿</div><div><div class="course-level level-b">Beginner</div><div class="course-title">Enscape Quick Start</div></div></div><div class="course-desc">Render settings, lighting, materials — professional renders from Revit in under an hour. No rendering experience required.</div><div class="course-meta"><div class="course-info">8 lessons · 2 hrs</div><button class="btn-start" onclick="showToast('Course coming soon!')">Start →</button></div></div>
            <div class="course-card reveal"><div class="course-top"><div class="course-icon lvl-i">📋</div><div><div class="course-level level-i">Intermediate</div><div class="course-title">Drawing Standards & QA</div></div></div><div class="course-desc">Layer naming, annotation styles, and QA checklist for professional engineering drawings. ISO + company standards compliance.</div><div class="course-meta"><div class="course-info">10 lessons · 2.5 hrs</div><button class="btn-start" onclick="showToast('Course coming soon!')">Start →</button></div></div>
            <div class="course-card reveal"><div class="course-top"><div class="course-icon lvl-a">🤖</div><div><div class="course-level level-a">Advanced</div><div class="course-title">AI Tools for Engineers</div></div></div><div class="course-desc">Use AI assistants, LISP generators, and Dynamo optimizers to 10x your productivity. Real-world automation using BIMZ tools.</div><div class="course-meta"><div class="course-info">15 lessons · 4 hrs</div><button class="btn-start" onclick="showToast('Course coming soon!')">Start →</button></div></div>
        </div>
    </div></div>
</div>
<div class="page" id="page-roadmap">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <div class="section-label">Development Plan</div>
            <h2 class="section-title">BIMZ Platform<br><span class="grad-text">Roadmap</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Transparent and updated — what's built, what's active, and what's coming next.</p>
        </div>
        <div class="roadmap-tl reveal">
            <div class="phase p-done"><div class="phase-dot">1</div><div class="phase-content"><div class="phase-header"><div class="phase-title">Core Tools Launch</div><span class="p-status s-done">✓ Done</span></div><div class="phase-sub">LISP scripts, AutoCAD fonts pack, Plot styles — all available for free download.</div></div></div>
            <div class="phase p-done"><div class="phase-dot">2</div><div class="phase-content"><div class="phase-header"><div class="phase-title">Dynamo & Revit Library</div><span class="p-status s-done">✓ Done</span></div><div class="phase-sub">Dynamo scripts, Revit families, Enscape presets, and MEP workflows.</div></div></div>
            <div class="phase p-active"><div class="phase-dot">3</div><div class="phase-content"><div class="phase-header"><div class="phase-title">BIMZ Platform Launch</div><span class="p-status s-active">⬤ Active</span></div><div class="phase-sub">Full BIMZ website, learning center, comparison guides, and tool ratings system.</div><div class="phase-date">In Progress — 2025</div></div></div>
            <div class="phase p-planned"><div class="phase-dot">4</div><div class="phase-content"><div class="phase-header"><div class="phase-title">BIMZ Bot — AI Assistant</div><span class="p-status s-planned">Planned</span></div><div class="phase-sub">Full AI chatbot for LISP, Dynamo & Revit. LISP Generator and Dynamo Optimizer.</div><div class="phase-date">Target: Q3 2025</div></div></div>
            <div class="phase p-planned"><div class="phase-dot">5</div><div class="phase-content"><div class="phase-header"><div class="phase-title">User Accounts & Dashboard</div><span class="p-status s-planned">Planned</span></div><div class="phase-sub">Favorites, download history, personal workspace, tool request system, and community.</div><div class="phase-date">Target: Q4 2025</div></div></div>
            <div class="phase p-planned"><div class="phase-dot">6</div><div class="phase-content"><div class="phase-header"><div class="phase-title">Multi-Language & Mobile App</div><span class="p-status s-planned">Planned</span></div><div class="phase-sub">8 languages: Arabic, English, Hindi, French, Spanish, German, Italian, Chinese + PWA app.</div><div class="phase-date">Target: 2026</div></div></div>
        </div>
    </div></div>
</div>
<div class="page" id="page-contact">
    <div class="section-pad"><div class="container">
        <div class="reveal">
            <div class="section-label">Get in Touch</div>
            <h2 class="section-title">Contact &amp;<br><span class="grad-text">Tool Requests</span></h2>
            <div class="divider"></div>
            <p class="section-sub">Need a specific tool, found a bug, or want to collaborate? Reach out directly — fast response guaranteed.</p>
        </div>
        <div class="contact-grid reveal">
            <div class="contact-cards">
                <a class="contact-card" href="mailto:ziadanter101@gmail.com"><div class="c-icon ic-email">✉️</div><div><div class="c-title">Email</div><div class="c-val">ziadanter101@gmail.com</div></div></a>
                <a class="contact-card" href="https://wa.me/201030718566" target="_blank"><div class="c-icon ic-wa">📱</div><div><div class="c-title">WhatsApp</div><div class="c-val">+20 103 071 8566</div></div></a>
                <div class="contact-card" style="cursor:default"><div class="c-icon ic-co">🏢</div><div><div class="c-title">Company</div><div class="c-val">DIRECT Consulting</div></div></div>
                <div class="contact-card" style="cursor:default"><div class="c-icon ic-sp">⚙️</div><div><div class="c-title">Specialization</div><div class="c-val">MEP — HVAC · Plumbing · Fire</div></div></div>
                <div class="contact-card" style="cursor:default"><div class="c-icon ic-web">🌐</div><div><div class="c-title">Platform</div><div class="c-val">bimz.tech <span style="font-size:.72rem;color:var(--text3)">(Coming Soon)</span></div></div></div>
            </div>
            <div class="contact-form">
                <div class="form-row"><div class="form-group"><label class="form-label">Your Name</label><input class="form-input" type="text" placeholder="Engineer Name"></div><div class="form-group"><label class="form-label">Email</label><input class="form-input" type="email" placeholder="you@company.com"></div></div>
                <div class="form-group"><label class="form-label">Request Type</label><select class="form-select"><option>🔧 Tool Request</option><option>🐛 Bug Report</option><option>🤝 Collaboration</option><option>💬 General Question</option><option>🎓 Learning Feedback</option></select></div>
                <div class="form-group"><label class="form-label">Message</label><textarea class="form-textarea" placeholder="Describe your request in detail..."></textarea></div>
                <button class="btn-submit" onclick="submitForm()">Send Message →</button>
            </div>
        </div>
    </div></div>
</div>
<footer id="site-footer">
    <div class="container">
        <div class="footer-grid">
            <div class="footer-brand">
                <svg width="36" height="36" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M50 10 L85 30 L85 70 L50 90 L15 70 L15 30 L50 10Z" stroke="#00d4ff" stroke-width="2" fill="none"/>
                    <text x="50" y="55" text-anchor="middle" fill="#00d4ff" font-size="24" font-weight="bold" font-family="Rajdhani">B</text>
                </svg>
                <p>The #1 AI-powered platform for BIM & CAD engineers. Free tools, smart automation, and structured learning — built by engineers, for engineers.</p>
            </div>
            <div class="footer-col"><h4>Tools</h4><a href="#" onclick="showPage('lisp');return false">LISP Scripts</a><a href="#" onclick="showPage('dynamo');return false">Dynamo Nodes</a><a href="#" onclick="showPage('revit');return false">Revit Families</a><a href="#" onclick="showPage('tools');return false">AutoCAD Fonts</a><a href="#" onclick="showPage('tools');return false">Plot Styles</a></div>
            <div class="footer-col"><h4>Platform</h4><a href="#" onclick="showPage('learning');return false">Learning Center</a><a href="#" onclick="showPage('ai');return false">BIMZ Bot</a><a href="#" onclick="showPage('roadmap');return false">Roadmap</a><a href="#" onclick="showPage('contact');return false">Request Tool</a><a href="#" onclick="showPage('contact');return false">Contact</a></div>
            <div class="footer-col"><h4>About</h4><a href="#">About Ziad</a><a href="#">DIRECT Consulting</a><a href="mailto:ziadanter101@gmail.com">Email Us</a><a href="https://wa.me/201030718566" target="_blank">WhatsApp</a></div>
        </div>
        <div class="footer-bottom">
            <div class="footer-copy">© 2025 BIMZ · By Ziad Technology · DIRECT Consulting · All tools free to use</div>
            <div class="footer-links"><a href="#">Privacy</a><a href="#">Terms</a><a href="#">Sitemap</a></div>
        </div>
    </div>
</footer>
<button id="back-top" onclick="window.scrollTo({top:0,behavior:'smooth'})">↑</button>
<div class="toast" id="toast"><span>✓</span><span id="toast-msg">Done!</span></div>
<script>
    const canvas=document.getElementById('bg-canvas'); const ctx=canvas.getContext('2d'); let pts=[];
    function rsz(){canvas.width=window.innerWidth;canvas.height=window.innerHeight}
    rsz(); window.addEventListener('resize',rsz);
    for(let i=0;i<70;i++)pts.push({x:Math.random()*window.innerWidth,y:Math.random()*window.innerHeight,r:Math.random()*1.2+.3,dx:(Math.random()-.5)*.25,dy:(Math.random()-.5)*.25,o:Math.random()*.4+.1});
    function draw(){ ctx.clearRect(0,0,canvas.width,canvas.height); pts.forEach(p=>{ ctx.beginPath();ctx.arc(p.x,p.y,p.r,0,Math.PI*2); ctx.fillStyle=`rgba(0,212,255,${p.o})`;ctx.fill(); p.x+=p.dx;p.y+=p.dy; if(p.x<0||p.x>canvas.width)p.dx*=-1; if(p.y<0||p.y>canvas.height)p.dy*=-1; }); for(let i=0;i<pts.length;i++)for(let j=i+1;j<pts.length;j++){ const d=Math.hypot(pts[i].x-pts[j].x,pts[i].y-pts[j].y); if(d<110){ctx.beginPath();ctx.moveTo(pts[i].x,pts[i].y);ctx.lineTo(pts[j].x,pts[j].y);ctx.strokeStyle=`rgba(0,212,255,${.05*(1-d/110)})`;ctx.lineWidth=.5;ctx.stroke()}} requestAnimationFrame(draw); }
    draw();
    function showPage(name){ document.querySelectorAll('.page').forEach(p=>p.classList.remove('active')); const pg=document.getElementById('page-'+name); if(pg){pg.classList.add('active');window.scrollTo({top:0,behavior:'smooth'});triggerReveal();} document.querySelectorAll('[data-page]').forEach(a=>a.classList.toggle('active-link',a.dataset.page===name)); }
    window.addEventListener('scroll',()=>{ const el=document.documentElement; const pct=(el.scrollTop/(el.scrollHeight-el.clientHeight))*100; document.getElementById('progress-bar').style.width=pct+'%'; document.getElementById('navbar').classList.toggle('scrolled',el.scrollTop>20); document.getElementById('back-top').classList.toggle('visible',el.scrollTop>300); });
    function triggerReveal(){ setTimeout(()=>{ const obs=new IntersectionObserver(entries=>entries.forEach(e=>{if(e.isIntersecting)e.target.classList.add('visible')}),{threshold:.1}); document.querySelectorAll('.reveal').forEach(el=>obs.observe(el)); },50); }
    triggerReveal();
    function toggleMobile(){document.getElementById('mobile-nav').classList.toggle('open')}
    function closeMobile(){document.getElementById('mobile-nav').classList.remove('open')}
    const cmdItems=[ {icon:'⚡',title:'LISP Scripts',sub:'AutoCAD automation tools',page:'lisp'}, {icon:'🔷',title:'Dynamo Scripts',sub:'Revit visual programming',page:'dynamo'}, {icon:'🏠',title:'Revit Families',sub:'Parametric MEP content',page:'revit'}, {icon:'🎨',title:'Plot Styles',sub:'CTB/STB files',page:'tools'}, {icon:'🔤',title:'AutoCAD Fonts',sub:'SHX + TTF pack',page:'tools'}, {icon:'🌿',title:'Enscape Tools',sub:'Render presets & AI',page:'tools'}, {icon:'🤖',title:'BIMZ Bot AI',sub:'Ask the AI assistant',page:'ai'}, {icon:'🎓',title:'Learning Center',sub:'Courses & learning paths',page:'learning'}, {icon:'🗺️',title:'Roadmap',sub:'Platform development plan',page:'roadmap'}, {icon:'✉️',title:'Contact',sub:'Get in touch / tool requests',page:'contact'}, ];
    let cmdActive=0;
    function openCmd(){document.getElementById('cmd-overlay').classList.add('open');document.getElementById('cmd-input').value='';renderCmd(cmdItems);setTimeout(()=>document.getElementById('cmd-input').focus(),50)}
    function closeCmd(e){if(e.target===document.getElementById('cmd-overlay'))document.getElementById('cmd-overlay').classList.remove('open')}
    function renderCmd(items){cmdActive=0;document.getElementById('cmd-results').innerHTML=items.map((item,i)=>`<div class="cmd-item${i===0?' active':''}" onclick="gotoCmd('${item.page}')"><div class="cmd-item-icon">${item.icon}</div><div><div class="cmd-item-title">${item.title}</div><div class="cmd-item-sub">${item.sub}</div></div></div>`).join('')}
    function filterCmd(v){const f=cmdItems.filter(i=>i.title.toLowerCase().includes(v.toLowerCase())||i.sub.toLowerCase().includes(v.toLowerCase()));renderCmd(f.length?f:cmdItems)}
    function gotoCmd(page){document.getElementById('cmd-overlay').classList.remove('open');showPage(page)}
    function cmdKey(e){const items=document.querySelectorAll('.cmd-item');if(e.key==='ArrowDown'){items[cmdActive]?.classList.remove('active');cmdActive=Math.min(cmdActive+1,items.length-1);items[cmdActive]?.classList.add('active');e.preventDefault()}if(e.key==='ArrowUp'){items[cmdActive]?.classList.remove('active');cmdActive=Math.max(cmdActive-1,0);items[cmdActive]?.classList.add('active');e.preventDefault()}if(e.key==='Enter')items[cmdActive]?.click();if(e.key==='Escape')document.getElementById('cmd-overlay').classList.remove('open')}
    document.addEventListener('keydown',e=>{if((e.metaKey||e.ctrlKey)&&e.key==='k'){e.preventDefault();openCmd()}if(e.key==='Escape')document.getElementById('cmd-overlay').classList.remove('open')});
    function filterTools(cat,btn){ document.querySelectorAll('.filter-tab').forEach(t=>t.classList.remove('active')); btn.classList.add('active'); document.querySelectorAll('.tool-card').forEach(c=>{c.style.display=(cat==='all'||c.dataset.cat?.includes(cat))?'':'none'}); }
    function toggleSave(btn){btn.classList.toggle('saved');btn.textContent=btn.classList.contains('saved')?'♥':'♡'}
    function showToast(msg){const t=document.getElementById('toast');document.getElementById('toast-msg').textContent=msg;t.classList.add('show');setTimeout(()=>t.classList.remove('show'),3000)}
    function submitForm(){showToast("Message sent! We'll reply soon.")}
    let chatHistory=[];
    const SYS=`You are BIMZ Bot — an expert AI assistant built exclusively for BIM and CAD engineers by Ziad Anter. Your expertise: AutoCAD LISP scripting, Dynamo for Revit, Revit families, MEP systems, Navisworks clash detection. Engineer-grade answers only. Always include code blocks for LISP. Respond in the same language as the question.`;
    async function sendMessage(){ const input=document.getElementById('chat-input'); const text=input.value.trim(); if(!text)return; appendMsg('user',text);input.value='';input.style.height=''; chatHistory.push({role:'user',content:text}); const btn=document.getElementById('send-btn');btn.disabled=true; const tid=appendTyping(); try{ const res=await fetch('https://api.anthropic.com/v1/messages',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({model:'claude-sonnet-4-20250514',max_tokens:1000,system:SYS,messages:chatHistory})}); const data=await res.json(); removeTyping(tid); const reply=data.content?.map(b=>b.text||'').join('')||'Sorry, could not get a response.'; chatHistory.push({role:'assistant',content:reply}); appendMsg('bot',reply); }catch(err){removeTyping(tid);appendMsg('bot','⚠️ Connection error. Please try again.');} btn.disabled=false; }
    function appendMsg(role,text){ const win=document.getElementById('chat-window'); const div=document.createElement('div');div.className='msg '+role; let fmt=text.replace(/```(\w+)?\n?([\s\S]*?)```/g,(_,l,c)=>`<pre>${escapeHtml(c.trim())}</pre>`); fmt=fmt.replace(/`([^`]+)`/g,'<code>$1</code>'); fmt=fmt.replace(/\*\*(.*?)\*\*/g,'<strong>$1</strong>'); fmt=fmt.replace(/\n/g,'<br>'); div.innerHTML=`<div class="msg-av">${role==='bot'?'🤖':'👤'}</div><div class="msg-bubble">${fmt}</div>`; win.appendChild(div);win.scrollTop=win.scrollHeight; }
    function escapeHtml(t){return t.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;')}
    function appendTyping(){ const win=document.getElementById('chat-window'); const id='t'+Date.now();const div=document.createElement('div');div.className='msg bot';div.id=id; div.innerHTML=`<div class="msg-av">🤖</div><div class="msg-bubble"><div class="typing"><span></span><span></span><span></span></div></div>`; win.appendChild(div);win.scrollTop=win.scrollHeight;return id; }
    function removeTyping(id){document.getElementById(id)?.remove()}
    function chatKeydown(e){if(e.key==='Enter'&&!e.shiftKey){e.preventDefault();sendMessage()}}
    function autoResize(el){el.style.height='auto';el.style.height=Math.min(el.scrollHeight,120)+'px'}
    function useSuggestion(el){document.getElementById('chat-input').value=el.textContent;document.getElementById('chat-input').focus()}
</script>
</body>
</html>
