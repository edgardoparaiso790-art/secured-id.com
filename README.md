import { useState, useRef } from "react";

const style = `
  @import url('https://secured-id.com);
  * { margin:0; padding:0; box-sizing:border-box; }
  :root { --gold:#c9a84c; --gold-light:#f0d080; --gold-dark:#7a5c1a; --navy:#060c1a; --navy2:#0a1428; --navy3:#0e1e3a; --cyan:#00d4ff; --cyan-dim:#007a9a; --white:#f0f4ff; --muted:#4a6080; }
  html { scroll-behavior: smooth; }
  body { background:var(--navy); color:var(--white); font-family:'Rajdhani',sans-serif; overflow-x:hidden; }
  @keyframes scanline { 0%{transform:translateY(-100%)} 100%{transform:translateY(100vh)} }
  @keyframes pulse-ring { 0%{transform:scale(1);opacity:.8} 100%{transform:scale(2.2);opacity:0} }
  @keyframes float { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-12px)} }
  @keyframes glow-text { 0%,100%{text-shadow:0 0 20px var(--gold),0 0 40px var(--gold-dark)} 50%{text-shadow:0 0 40px var(--gold-light),0 0 80px var(--gold)} }
  @keyframes slide-in-left { from{opacity:0;transform:translateX(-60px)} to{opacity:1;transform:translateX(0)} }
  @keyframes slide-in-right { from{opacity:0;transform:translateX(60px)} to{opacity:1;transform:translateX(0)} }
  @keyframes slide-in-up { from{opacity:0;transform:translateY(40px)} to{opacity:1;transform:translateY(0)} }
  @keyframes counter-up { from{opacity:0;transform:translateY(20px) scale(.8)} to{opacity:1;transform:translateY(0) scale(1)} }
  @keyframes spin-slow { from{transform:rotate(0deg)} to{transform:rotate(360deg)} }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0} }
  @keyframes ticker { 0%{transform:translateX(0)} 100%{transform:translateX(-50%)} }
  @keyframes fade-in { from{opacity:0} to{opacity:1} }

  .scanline-overlay { position:fixed;top:0;left:0;width:100%;height:60px;background:linear-gradient(to bottom,transparent,rgba(0,212,255,.04),transparent);animation:scanline 4s linear infinite;pointer-events:none;z-index:9999; }
  .navbar { position:fixed;top:0;left:0;right:0;z-index:100;background:rgba(6,12,26,.92);backdrop-filter:blur(12px);border-bottom:1px solid var(--gold-dark);padding:0 40px;height:64px;display:flex;align-items:center;justify-content:space-between; }
  .nav-logo { font-family:'Bebas Neue',sans-serif;font-size:22px;letter-spacing:4px;color:var(--gold);display:flex;align-items:center;gap:10px;cursor:pointer; }
  .nav-links { display:flex;gap:32px; }
  .nav-links a { font-family:'Share Tech Mono',monospace;font-size:12px;color:var(--muted);text-decoration:none;letter-spacing:2px;text-transform:uppercase;transition:color .2s;cursor:pointer;padding-bottom:2px;border-bottom:1px solid transparent; }
  .nav-links a:hover { color:var(--gold); }
  .nav-links a.active { color:var(--gold);border-bottom:1px solid var(--gold); }
  .nav-cta { background:linear-gradient(135deg,var(--gold-dark),var(--gold));color:var(--navy);font-family:'Bebas Neue',sans-serif;font-size:14px;letter-spacing:3px;padding:8px 20px;border:none;border-radius:4px;cursor:pointer;transition:transform .2s,box-shadow .2s; }
  .nav-cta:hover { transform:translateY(-2px);box-shadow:0 4px 20px var(--gold-dark); }
  .ticker-bar { margin-top:64px;background:var(--gold-dark);overflow:hidden;height:32px;display:flex;align-items:center; }
  .ticker-label { background:var(--gold);color:var(--navy);font-family:'Bebas Neue',sans-serif;font-size:14px;letter-spacing:3px;padding:0 16px;height:100%;display:flex;align-items:center;white-space:nowrap;flex-shrink:0; }
  .ticker-content { display:flex;animation:ticker 28s linear infinite;white-space:nowrap; }
  .ticker-item { font-family:'Share Tech Mono',monospace;font-size:11px;color:var(--gold-light);padding:0 32px;letter-spacing:1px; }
  .ticker-sep { color:var(--gold);padding:0 8px; }
  .hero { min-height:100vh;display:flex;align-items:center;position:relative;overflow:hidden;background:radial-gradient(ellipse at 30% 50%,#0e2244,var(--navy) 60%);padding:40px; }
  .hero-grid { position:absolute;inset:0;opacity:.07;background-image:linear-gradient(var(--cyan) 1px,transparent 1px),linear-gradient(90deg,var(--cyan) 1px,transparent 1px);background-size:60px 60px; }
  .hero-glow { position:absolute;top:20%;left:10%;width:500px;height:500px;background:radial-gradient(circle,rgba(201,168,76,.12),transparent 70%);pointer-events:none; }
  .hero-glow2 { position:absolute;bottom:10%;right:5%;width:400px;height:400px;background:radial-gradient(circle,rgba(0,212,255,.08),transparent 70%);pointer-events:none; }
  .hero-inner { max-width:1200px;margin:0 auto;width:100%;display:grid;grid-template-columns:1fr 1fr;gap:60px;align-items:center; }
  .hero-badge { display:inline-flex;align-items:center;gap:8px;background:rgba(201,168,76,.1);border:1px solid var(--gold-dark);border-radius:2px;padding:6px 14px;margin-bottom:24px;animation:slide-in-left .8s ease forwards; }
  .hero-badge-dot { width:6px;height:6px;border-radius:50%;background:var(--gold);animation:blink 1.5s infinite; }
  .hero-badge span { font-family:'Share Tech Mono',monospace;font-size:11px;color:var(--gold);letter-spacing:2px; }
  .hero-h1 { font-family:'Bebas Neue',sans-serif;font-size:clamp(48px,7vw,88px);line-height:.92;letter-spacing:3px;animation:slide-in-left .8s .1s ease both; }
  .hero-h1 .gold { color:var(--gold);animation:glow-text 3s ease-in-out infinite; }
  .hero-h1 .cyan { color:var(--cyan); }
  .hero-sub { font-size:16px;color:#7090b0;line-height:1.7;margin:20px 0 36px;max-width:440px;animation:slide-in-left .8s .2s ease both; }
  .hero-actions { display:flex;gap:16px;flex-wrap:wrap;animation:slide-in-left .8s .3s ease both; }
  .btn-primary { background:linear-gradient(135deg,#7a5c1a,var(--gold));color:var(--navy);font-family:'Bebas Neue',sans-serif;font-size:17px;letter-spacing:3px;padding:14px 36px;border:none;border-radius:4px;cursor:pointer;transition:all .25s; }
  .btn-primary:hover { transform:translateY(-3px);box-shadow:0 8px 30px rgba(201,168,76,.4); }
  .btn-outline { background:transparent;color:var(--cyan);border:1px solid var(--cyan-dim);font-family:'Bebas Neue',sans-serif;font-size:17px;letter-spacing:3px;padding:14px 36px;border-radius:4px;cursor:pointer;transition:all .25s; }
  .btn-outline:hover { background:rgba(0,212,255,.08);border-color:var(--cyan);transform:translateY(-3px); }
  .hero-visual { display:flex;justify-content:center;align-items:center;animation:slide-in-right .8s .2s ease both; }
  .shield-wrap { position:relative;width:320px;height:320px;animation:float 4s ease-in-out infinite; }
  .pulse-ring { position:absolute;inset:-20px;border-radius:50%;border:2px solid var(--gold);animation:pulse-ring 2.4s ease-out infinite; }
  .pulse-ring2 { animation-delay:1.2s; }
  .shield-outer { width:320px;height:320px;border-radius:50%;background:conic-gradient(from 0deg,var(--gold-dark),var(--navy3),var(--gold-dark),var(--navy3),var(--gold-dark));animation:spin-slow 12s linear infinite;display:flex;align-items:center;justify-content:center; }
  .shield-inner { width:280px;height:280px;border-radius:50%;background:radial-gradient(circle at 35% 35%,#0e2244,var(--navy));display:flex;align-items:center;justify-content:center;flex-direction:column;gap:10px;border:1px solid var(--gold-dark); }
  .shield-icon { font-size:72px;filter:drop-shadow(0 0 20px var(--gold)); }
  .shield-ref { font-family:'Share Tech Mono',monospace;font-size:12px;color:var(--gold);letter-spacing:2px; }
  .shield-status { display:flex;align-items:center;gap:6px;font-family:'Share Tech Mono',monospace;font-size:10px;color:#00e676; }
  .status-dot { width:6px;height:6px;border-radius:50%;background:#00e676;animation:blink 1s infinite; }
  .stats-strip { background:var(--navy3);border-top:1px solid var(--gold-dark);border-bottom:1px solid var(--gold-dark);padding:40px; }
  .stats-inner { max-width:1200px;margin:0 auto;display:grid;grid-template-columns:repeat(4,1fr); }
  .stat-item { text-align:center;padding:20px;border-right:1px solid #1a3050;animation:counter-up .8s ease both; }
  .stat-item:last-child { border-right:none; }
  .stat-num { font-family:'Bebas Neue',sans-serif;font-size:52px;color:var(--gold);line-height:1;letter-spacing:2px; }
  .stat-unit { font-family:'Bebas Neue',sans-serif;font-size:28px;color:var(--gold-light); }
  .stat-label { font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);letter-spacing:2px;margin-top:6px; }
  .section { padding:100px 40px;scroll-margin-top:72px; }
  .section-inner { max-width:1200px;margin:0 auto; }
  .section-eyebrow { font-family:'Share Tech Mono',monospace;font-size:11px;letter-spacing:4px;color:var(--cyan);text-transform:uppercase;margin-bottom:12px; }
  .section-title { font-family:'Bebas Neue',sans-serif;font-size:clamp(38px,5vw,60px);letter-spacing:3px;color:var(--white);margin-bottom:16px; }
  .section-title .gold { color:var(--gold); }
  .section-desc { font-size:16px;color:var(--muted);max-width:520px;line-height:1.7;margin-bottom:60px; }
  .steps-grid { display:grid;grid-template-columns:repeat(4,1fr);gap:24px;position:relative; }
  .steps-grid::before { content:'';position:absolute;top:48px;left:12%;right:12%;height:1px;background:linear-gradient(90deg,transparent,var(--gold-dark),var(--gold),var(--gold-dark),transparent); }
  .step-card { background:linear-gradient(135deg,var(--navy3),var(--navy2));border:1px solid #1a3050;border-radius:8px;padding:28px 22px;text-align:center;transition:all .3s;cursor:default; }
  .step-card:hover { border-color:var(--gold-dark);transform:translateY(-6px);box-shadow:0 12px 40px rgba(201,168,76,.12); }
  .step-num { width:56px;height:56px;border-radius:50%;margin:0 auto 18px;background:linear-gradient(135deg,var(--gold-dark),var(--gold));display:flex;align-items:center;justify-content:center;font-family:'Bebas Neue',sans-serif;font-size:22px;color:var(--navy);position:relative;z-index:1; }
  .step-icon { font-size:24px;margin-bottom:12px; }
  .step-title { font-family:'Bebas Neue',sans-serif;font-size:20px;letter-spacing:2px;color:var(--gold-light);margin-bottom:10px; }
  .step-desc { font-size:13px;color:var(--muted);line-height:1.6; }
  .features-section { padding:100px 40px;scroll-margin-top:72px;background:linear-gradient(180deg,var(--navy),var(--navy3) 50%,var(--navy)); }
  .features-grid { display:grid;grid-template-columns:repeat(3,1fr);gap:28px; }
  .feature-card { background:rgba(14,30,58,.6);border:1px solid #1a3050;border-radius:10px;padding:32px 28px;transition:all .3s;cursor:default;position:relative;overflow:hidden; }
  .feature-card::before { content:'';position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,var(--gold),transparent);opacity:0;transition:opacity .3s; }
  .feature-card:hover::before { opacity:1; }
  .feature-card:hover { border-color:#2a4a70;transform:translateY(-4px);box-shadow:0 16px 48px rgba(0,0,0,.4); }
  .feature-ico { font-size:36px;margin-bottom:16px; }
  .feature-title { font-family:'Bebas Neue',sans-serif;font-size:22px;letter-spacing:2px;color:var(--gold-light);margin-bottom:10px; }
  .feature-desc { font-size:14px;color:var(--muted);line-height:1.65; }
  .feature-tag { display:inline-block;margin-top:14px;background:rgba(0,212,255,.08);border:1px solid var(--cyan-dim);color:var(--cyan);font-family:'Share Tech Mono',monospace;font-size:10px;letter-spacing:2px;padding:4px 10px;border-radius:2px; }
  .demo-section { padding:100px 40px;scroll-margin-top:72px; }
  .demo-card { max-width:680px;margin:0 auto;background:linear-gradient(135deg,var(--navy3),var(--navy2));border:1px solid var(--gold-dark);border-radius:12px;overflow:hidden;box-shadow:0 0 60px rgba(201,168,76,.08); }
  .demo-header { background:linear-gradient(90deg,#0e1e3a,#162a50);padding:16px 24px;border-bottom:1px solid var(--gold-dark);display:flex;align-items:center;justify-content:space-between; }
  .demo-header-title { font-family:'Share Tech Mono',monospace;font-size:12px;color:var(--gold);letter-spacing:2px; }
  .demo-dots { display:flex;gap:6px; }
  .demo-dot { width:10px;height:10px;border-radius:50%; }
  .demo-body { padding:36px; }
  .demo-field { margin-bottom:20px; }
  .demo-field label { display:block;font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--cyan-dim);letter-spacing:2px;margin-bottom:6px; }
  .demo-field .value { background:rgba(0,0,0,.3);border:1px solid #1a3050;border-radius:4px;padding:12px 14px;font-family:'Share Tech Mono',monospace;font-size:13px;color:var(--white); }
  .demo-field .value.ref { color:var(--gold);font-size:16px;letter-spacing:3px; }
  .demo-field .value.verified { color:#00e676; }
  .demo-row { display:grid;grid-template-columns:1fr 1fr;gap:20px; }
  .demo-actions { display:flex;gap:12px;margin-top:28px; }
  .demo-btn { flex:1;padding:12px;border-radius:4px;font-family:'Bebas Neue',sans-serif;font-size:16px;letter-spacing:2px;cursor:pointer;transition:all .2s;border:none; }
  .demo-btn.primary { background:linear-gradient(135deg,var(--gold-dark),var(--gold));color:var(--navy); }
  .demo-btn.secondary { background:transparent;border:1px solid var(--muted);color:var(--muted); }
  .demo-btn:hover { transform:translateY(-2px); }
  .demo-badge { display:flex;align-items:center;gap:8px;margin-top:20px;background:rgba(0,230,118,.06);border:1px solid rgba(0,230,118,.2);border-radius:4px;padding:10px 14px; }
  .demo-badge-dot { width:8px;height:8px;border-radius:50%;background:#00e676;animation:blink 1.2s infinite; }
  .demo-badge span { font-family:'Share Tech Mono',monospace;font-size:11px;color:#00e676;letter-spacing:1px; }
  .testimonials { padding:100px 40px;background:var(--navy3); }
  .test-grid { display:grid;grid-template-columns:repeat(3,1fr);gap:28px; }
  .test-card { background:var(--navy2);border:1px solid #1a3050;border-radius:10px;padding:28px;transition:all .3s; }
  .test-card:hover { border-color:var(--gold-dark); }
  .test-quote { font-size:14px;color:#8090a0;line-height:1.7;margin-bottom:20px;font-style:italic; }
  .test-author { display:flex;align-items:center;gap:12px; }
  .test-avatar { width:42px;height:42px;border-radius:50%;background:linear-gradient(135deg,var(--gold-dark),var(--gold));display:flex;align-items:center;justify-content:center;font-family:'Bebas Neue',sans-serif;font-size:16px;color:var(--navy); }
  .test-name { font-family:'Rajdhani',sans-serif;font-weight:700;font-size:15px;color:var(--white); }
  .test-role { font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);letter-spacing:1px; }
  .test-stars { color:var(--gold);font-size:12px;margin-bottom:12px; }
  .pricing { padding:100px 40px;scroll-margin-top:72px; }
  .pricing-grid { display:grid;grid-template-columns:repeat(3,1fr);gap:28px;max-width:1000px;margin:0 auto; }
  .plan-card { border:1px solid #1a3050;border-radius:12px;padding:36px 28px;text-align:center;background:linear-gradient(135deg,var(--navy3),var(--navy2));transition:all .3s;position:relative;overflow:hidden; }
  .plan-card.featured { border-color:var(--gold);transform:scale(1.04);box-shadow:0 0 60px rgba(201,168,76,.18); }
  .plan-badge { position:absolute;top:16px;right:-24px;background:var(--gold);color:var(--navy);font-family:'Bebas Neue',sans-serif;font-size:12px;letter-spacing:2px;padding:4px 32px;transform:rotate(35deg); }
  .plan-name { font-family:'Bebas Neue',sans-serif;font-size:26px;letter-spacing:3px;color:var(--gold-light);margin-bottom:8px; }
  .plan-price { font-family:'Bebas Neue',sans-serif;font-size:56px;color:var(--gold);line-height:1; }
  .plan-period { font-family:'Share Tech Mono',monospace;font-size:11px;color:var(--muted);letter-spacing:2px;margin-bottom:28px; }
  .plan-features { list-style:none;text-align:left;margin-bottom:28px; }
  .plan-features li { padding:8px 0;border-bottom:1px solid #1a3050;font-size:13px;color:#7090a8;display:flex;align-items:center;gap:8px; }
  .plan-features li .check { color:#00e676;font-size:14px; }
  .plan-btn { width:100%;padding:13px;font-family:'Bebas Neue',sans-serif;font-size:17px;letter-spacing:3px;border-radius:4px;cursor:pointer;transition:all .25s;border:none; }
  .plan-btn.outline { background:transparent;border:1px solid var(--gold-dark);color:var(--gold); }
  .plan-btn.fill { background:linear-gradient(135deg,var(--gold-dark),var(--gold));color:var(--navy); }
  .plan-btn:hover { transform:translateY(-2px);box-shadow:0 6px 24px rgba(201,168,76,.25); }
  .contact-section { padding:100px 40px; scroll-margin-top:72px; background:linear-gradient(180deg,var(--navy),var(--navy3)); }
  .contact-grid { display:grid; grid-template-columns:1fr 1.4fr; gap:60px; align-items:start; max-width:1000px; margin:0 auto; }
  .contact-info-item { display:flex; align-items:flex-start; gap:14px; margin-bottom:28px; }
  .contact-ico { width:44px; height:44px; border-radius:8px; background:linear-gradient(135deg,var(--gold-dark),var(--gold)); display:flex; align-items:center; justify-content:center; font-size:20px; flex-shrink:0; }
  .contact-info-label { font-family:'Share Tech Mono',monospace; font-size:10px; color:var(--cyan-dim); letter-spacing:2px; margin-bottom:4px; }
  .contact-info-val { font-family:'Rajdhani',sans-serif; font-weight:600; font-size:15px; color:var(--white); }
  .contact-form-card { background:linear-gradient(135deg,var(--navy3),var(--navy2)); border:1px solid var(--gold-dark); border-radius:14px; overflow:hidden; box-shadow:0 0 50px rgba(201,168,76,.07); }
  .contact-form-header { background:linear-gradient(90deg,#0e1e3a,#162a50); padding:16px 24px; border-bottom:1px solid var(--gold-dark); display:flex; align-items:center; gap:10px; }
  .contact-form-title { font-family:'Bebas Neue',sans-serif; font-size:16px; letter-spacing:3px; color:var(--gold); }
  .contact-form-body { padding:28px; }
  .cf-row { display:grid; grid-template-columns:1fr 1fr; gap:16px; }
  .cf-field { margin-bottom:16px; }
  .cf-field label { display:block; font-family:'Share Tech Mono',monospace; font-size:10px; color:var(--cyan-dim); letter-spacing:2px; margin-bottom:6px; }
  .cf-input { width:100%; background:rgba(0,0,0,.3); border:1px solid #1a3050; border-radius:6px; padding:11px 14px; font-family:'Rajdhani',sans-serif; font-size:14px; color:var(--white); outline:none; transition:border-color .2s; box-sizing:border-box; }
  .cf-input:focus { border-color:var(--gold-dark); }
  .cf-input.err { border-color:#ff4444; }
  .cf-textarea { resize:vertical; min-height:110px; }
  .cf-err { font-family:'Share Tech Mono',monospace; font-size:10px; color:#ff6666; margin-top:4px; letter-spacing:1px; }
  .cf-select { appearance:none; background:rgba(0,0,0,.3) url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='10' height='6'%3E%3Cpath d='M0 0l5 6 5-6z' fill='%23c9a84c'/%3E%3C/svg%3E") no-repeat right 14px center; }
  .cf-submit { width:100%; padding:14px; background:linear-gradient(135deg,var(--gold-dark),var(--gold)); color:var(--navy); font-family:'Bebas Neue',sans-serif; font-size:18px; letter-spacing:3px; border:none; border-radius:6px; cursor:pointer; transition:all .25s; margin-top:4px; }
  .cf-submit:hover { transform:translateY(-2px); box-shadow:0 8px 28px rgba(201,168,76,.35); }
  .cf-submit:disabled { opacity:.5; cursor:default; transform:none; }
  .cf-success { text-align:center; padding:20px 0; }
  .cf-success-ico { font-size:48px; display:block; margin-bottom:12px; }
  .cf-success-title { font-family:'Bebas Neue',sans-serif; font-size:26px; letter-spacing:3px; color:var(--gold); margin-bottom:8px; }
  .cf-success-note { font-family:'Share Tech Mono',monospace; font-size:11px; color:var(--muted); line-height:1.7; letter-spacing:1px; }
  .cf-reset { margin-top:16px; background:transparent; border:1px solid var(--gold-dark); color:var(--gold); font-family:'Bebas Neue',sans-serif; font-size:15px; letter-spacing:2px; padding:10px 24px; border-radius:6px; cursor:pointer; transition:all .2s; }
  .cf-reset:hover { background:rgba(201,168,76,.1); }
  @media(max-width:768px) { .contact-grid{grid-template-columns:1fr} .cf-row{grid-template-columns:1fr} }
background:linear-gradient(135deg,#0a1e3c,#0e2850 50%,#0a1e3c);border-top:1px solid var(--gold-dark);border-bottom:1px solid var(--gold-dark);text-align:center; }
  .cta-title { font-family:'Bebas Neue',sans-serif;font-size:clamp(42px,6vw,72px);letter-spacing:4px;color:var(--white);margin-bottom:16px; }
  .cta-title .gold { color:var(--gold);animation:glow-text 3s ease-in-out infinite; }
  .cta-sub { font-size:16px;color:var(--muted);margin-bottom:40px; }
  .cta-btns { display:flex;gap:16px;justify-content:center;flex-wrap:wrap; }
  .cta-trust { margin-top:28px;display:flex;gap:32px;justify-content:center;flex-wrap:wrap; }
  .trust-item { display:flex;align-items:center;gap:8px;font-family:'Share Tech Mono',monospace;font-size:11px;color:var(--muted);letter-spacing:1px; }
  .trust-icon { color:var(--gold); }
  footer { scroll-margin-top:72px;background:#040810;border-top:1px solid #0d1e38;padding:40px;text-align:center; }
  .footer-logo { font-family:'Bebas Neue',sans-serif;font-size:24px;letter-spacing:4px;color:var(--gold);margin-bottom:12px; }
  .footer-links { display:flex;gap:28px;justify-content:center;flex-wrap:wrap;margin-bottom:20px; }
  .footer-links a { font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);letter-spacing:2px;text-decoration:none;cursor:pointer; }
  .footer-links a:hover { color:var(--gold); }
  .footer-legal { font-family:'Share Tech Mono',monospace;font-size:10px;color:#2a3a50;letter-spacing:1px; }
  @media(max-width:768px){.hero-inner{grid-template-columns:1fr}.hero-visual{display:none}.stats-inner{grid-template-columns:repeat(2,1fr)}.steps-grid{grid-template-columns:1fr 1fr}.steps-grid::before{display:none}.features-grid{grid-template-columns:1fr}.test-grid{grid-template-columns:1fr}.pricing-grid{grid-template-columns:1fr}.plan-card.featured{transform:scale(1)}.demo-row{grid-template-columns:1fr}.navbar{padding:0 20px}.nav-links{display:none}}
`;

const AI_STYLE = `
  .ai-fab{position:fixed;bottom:32px;right:32px;z-index:9999;width:60px;height:60px;border-radius:50%;background:linear-gradient(135deg,var(--gold-dark),var(--gold));border:none;cursor:pointer;font-size:26px;box-shadow:0 4px 24px rgba(201,168,76,.45);transition:transform .2s,box-shadow .2s;display:flex;align-items:center;justify-content:center;}
  .ai-fab:hover{transform:scale(1.1);box-shadow:0 8px 36px rgba(201,168,76,.6);}
  .ai-fab .notif{position:absolute;top:2px;right:2px;width:14px;height:14px;border-radius:50%;background:#00e676;border:2px solid var(--navy);animation:blink 1.5s infinite;}
  .ai-panel{position:fixed;bottom:104px;right:32px;z-index:9998;width:370px;max-height:540px;background:linear-gradient(135deg,var(--navy3),var(--navy2));border:1px solid var(--gold-dark);border-radius:14px;display:flex;flex-direction:column;overflow:hidden;box-shadow:0 16px 60px rgba(0,0,0,.6);animation:slide-in-up .3s ease both;}
  .ai-header{background:linear-gradient(90deg,#0e1e3a,#162a50);padding:14px 18px;border-bottom:1px solid var(--gold-dark);display:flex;align-items:center;justify-content:space-between;}
  .ai-header-left{display:flex;align-items:center;gap:10px;}
  .ai-avatar{width:36px;height:36px;border-radius:50%;background:linear-gradient(135deg,var(--gold-dark),var(--gold));display:flex;align-items:center;justify-content:center;font-size:18px;}
  .ai-name{font-family:'Bebas Neue',sans-serif;font-size:16px;letter-spacing:2px;color:var(--gold-light);}
  .ai-status{display:flex;align-items:center;gap:5px;font-family:'Share Tech Mono',monospace;font-size:9px;color:#00e676;}
  .ai-status-dot{width:5px;height:5px;border-radius:50%;background:#00e676;animation:blink 1.2s infinite;}
  .ai-close{background:none;border:none;color:var(--muted);font-size:20px;cursor:pointer;line-height:1;padding:0 4px;transition:color .2s;}
  .ai-close:hover{color:var(--white);}
  .ai-messages{flex:1;overflow-y:auto;padding:18px;display:flex;flex-direction:column;gap:12px;scrollbar-width:thin;scrollbar-color:var(--gold-dark) transparent;}
  .ai-msg{display:flex;gap:8px;animation:fade-in .3s ease;}
  .ai-msg.user{flex-direction:row-reverse;}
  .ai-msg-bubble{max-width:82%;padding:10px 14px;border-radius:10px;font-size:13px;line-height:1.55;}
  .ai-msg.bot .ai-msg-bubble{background:rgba(14,30,58,.8);border:1px solid #1a3050;color:#c0d0e0;}
  .ai-msg.user .ai-msg-bubble{background:linear-gradient(135deg,var(--gold-dark),#a07828);color:var(--navy);font-weight:600;}
  .ai-msg-ico{width:28px;height:28px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:14px;flex-shrink:0;margin-top:2px;}
  .ai-msg.bot .ai-msg-ico{background:linear-gradient(135deg,var(--gold-dark),var(--gold));}
  .ai-msg.user .ai-msg-ico{background:#1a3050;}
  .ai-typing{display:flex;gap:4px;align-items:center;padding:10px 14px;}
  .ai-typing span{width:7px;height:7px;border-radius:50%;background:var(--gold-dark);animation:blink 1s infinite;}
  .ai-typing span:nth-child(2){animation-delay:.2s;}.ai-typing span:nth-child(3){animation-delay:.4s;}
  .ai-chips{padding:0 18px 12px;display:flex;flex-wrap:wrap;gap:7px;}
  .ai-chip{background:rgba(201,168,76,.08);border:1px solid var(--gold-dark);color:var(--gold-light);font-family:'Share Tech Mono',monospace;font-size:10px;letter-spacing:1px;padding:5px 10px;border-radius:20px;cursor:pointer;transition:all .2s;}
  .ai-chip:hover{background:rgba(201,168,76,.18);border-color:var(--gold);}
  .ai-input-row{padding:12px 14px;border-top:1px solid #1a3050;display:flex;gap:8px;}
  .ai-input{flex:1;background:rgba(0,0,0,.3);border:1px solid #1a3050;border-radius:6px;padding:9px 12px;font-family:'Rajdhani',sans-serif;font-size:13px;color:var(--white);outline:none;transition:border-color .2s;}
  .ai-input:focus{border-color:var(--gold-dark);}
  .ai-input::placeholder{color:var(--muted);}
  .ai-send{background:linear-gradient(135deg,var(--gold-dark),var(--gold));border:none;border-radius:6px;width:36px;height:36px;cursor:pointer;font-size:15px;transition:transform .2s;display:flex;align-items:center;justify-content:center;}
  .ai-send:hover{transform:scale(1.08);}
  .ai-send:disabled{opacity:.5;cursor:default;transform:none;}
  @media(max-width:768px){.ai-panel{width:calc(100vw - 32px);right:16px;bottom:90px}.ai-fab{right:16px;bottom:20px}}
`;

const REG_STYLE = `
  .reg-overlay{position:fixed;inset:0;z-index:10000;background:rgba(4,8,18,.93);backdrop-filter:blur(8px);display:flex;align-items:center;justify-content:center;padding:20px;animation:fade-in .25s ease;}
  .reg-modal{width:100%;max-width:480px;background:linear-gradient(135deg,#0e1e3a,#0a1428);border:1px solid var(--gold-dark);border-radius:16px;overflow:hidden;box-shadow:0 24px 80px rgba(0,0,0,.7);animation:slide-in-up .3s ease;}
  .reg-header{background:linear-gradient(90deg,#0e1e3a,#162a50);padding:20px 28px;border-bottom:1px solid var(--gold-dark);display:flex;align-items:center;justify-content:space-between;}
  .reg-logo{font-family:'Bebas Neue',sans-serif;font-size:20px;letter-spacing:3px;color:var(--gold);}
  .reg-x{background:none;border:none;color:var(--muted);font-size:22px;cursor:pointer;line-height:1;padding:0;transition:color .2s;}
  .reg-x:hover{color:var(--white);}
  .reg-progress{padding:20px 28px 0;}
  .reg-bars{display:flex;gap:8px;margin-bottom:6px;}
  .reg-bar{flex:1;height:3px;border-radius:2px;background:#1a3050;transition:background .4s;}
  .reg-bar.done{background:var(--gold);}
  .reg-bar.active{background:linear-gradient(90deg,var(--gold-dark),var(--gold));}
  .reg-step-lbl{font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);letter-spacing:2px;}
  .reg-body{padding:24px 28px 28px;}
  .reg-title{font-family:'Bebas Neue',sans-serif;font-size:26px;letter-spacing:3px;color:var(--gold-light);margin-bottom:6px;}
  .reg-sub{font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);letter-spacing:1px;margin-bottom:22px;}
  .reg-field{margin-bottom:16px;}
  .reg-field label{display:block;font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--cyan-dim);letter-spacing:2px;margin-bottom:6px;}
  .reg-input{width:100%;background:rgba(0,0,0,.3);border:1px solid #1a3050;border-radius:6px;padding:11px 14px;font-family:'Rajdhani',sans-serif;font-size:14px;color:var(--white);outline:none;transition:border-color .2s;box-sizing:border-box;}
  .reg-input:focus{border-color:var(--gold-dark);}
  .reg-input.err{border-color:#ff4444;}
  .reg-err{font-family:'Share Tech Mono',monospace;font-size:10px;color:#ff6666;margin-top:5px;letter-spacing:1px;}
  .reg-plans{display:flex;flex-direction:column;gap:10px;}
  .reg-plan{border:1px solid #1a3050;border-radius:8px;padding:14px 16px;cursor:pointer;transition:all .2s;display:flex;align-items:center;gap:14px;}
  .reg-plan.sel{border-color:var(--gold);background:rgba(201,168,76,.06);}
  .reg-radio{width:16px;height:16px;border-radius:50%;border:2px solid var(--muted);display:flex;align-items:center;justify-content:center;flex-shrink:0;transition:all .2s;}
  .reg-plan.sel .reg-radio{border-color:var(--gold);background:var(--gold);}
  .reg-dot{width:6px;height:6px;border-radius:50%;background:var(--navy);}
  .reg-pname{font-family:'Bebas Neue',sans-serif;font-size:18px;letter-spacing:2px;color:var(--gold-light);}
  .reg-pprice{font-family:'Share Tech Mono',monospace;font-size:11px;color:var(--muted);}
  .reg-pop{margin-left:auto;background:rgba(201,168,76,.15);border:1px solid var(--gold-dark);color:var(--gold);font-family:'Share Tech Mono',monospace;font-size:9px;letter-spacing:1px;padding:3px 8px;border-radius:2px;}
  .reg-actions{display:flex;gap:10px;margin-top:22px;}
  .reg-back{flex:0 0 auto;background:transparent;border:1px solid #1a3050;color:var(--muted);font-family:'Bebas Neue',sans-serif;font-size:16px;letter-spacing:2px;padding:12px 20px;border-radius:6px;cursor:pointer;transition:all .2s;}
  .reg-back:hover{border-color:var(--muted);color:var(--white);}
  .reg-next{flex:1;background:linear-gradient(135deg,var(--gold-dark),var(--gold));color:var(--navy);font-family:'Bebas Neue',sans-serif;font-size:17px;letter-spacing:3px;padding:13px;border:none;border-radius:6px;cursor:pointer;transition:all .2s;}
  .reg-next:hover{transform:translateY(-2px);box-shadow:0 6px 24px rgba(201,168,76,.3);}
  .reg-next:disabled{opacity:.5;cursor:default;transform:none;}
  .reg-success{text-align:center;padding:8px 0;}
  .reg-sico{font-size:54px;display:block;margin-bottom:14px;animation:float 3s ease-in-out infinite;}
  .reg-stitle{font-family:'Bebas Neue',sans-serif;font-size:30px;letter-spacing:4px;color:var(--gold);animation:glow-text 3s ease-in-out infinite;margin-bottom:8px;}
  .reg-str-box{background:rgba(0,0,0,.35);border:1px solid var(--gold-dark);border-radius:8px;padding:16px;margin:18px 0;}
  .reg-str-lbl{font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--cyan-dim);letter-spacing:2px;margin-bottom:8px;}
  .reg-str-val{font-family:'Share Tech Mono',monospace;font-size:20px;color:var(--gold);letter-spacing:3px;word-break:break-all;}
  .reg-str-copy{margin-top:10px;background:rgba(201,168,76,.1);border:1px solid var(--gold-dark);color:var(--gold);font-family:'Share Tech Mono',monospace;font-size:10px;letter-spacing:2px;padding:6px 16px;border-radius:4px;cursor:pointer;transition:all .2s;}
  .reg-str-copy:hover{background:rgba(201,168,76,.2);}
  .reg-snote{font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);line-height:1.7;letter-spacing:1px;}
  .reg-chk-row{display:flex;align-items:flex-start;gap:10px;margin-top:4px;}
  .reg-chk{width:16px;height:16px;accent-color:var(--gold);cursor:pointer;flex-shrink:0;margin-top:2px;}
  .reg-chk-lbl{font-family:'Share Tech Mono',monospace;font-size:10px;color:var(--muted);letter-spacing:1px;line-height:1.6;}
  .reg-chk-lbl a{color:var(--gold);text-decoration:none;}
`;

// ── DATA ──────────────────────────────────────────────────────────────────────
const tickerItems = ["🔐 256-BIT ENCRYPTION ACTIVE","✅ ISO 27001 CERTIFIED","🛡️ GDPR COMPLIANT","⚡ 99.97% UPTIME SLA","🌍 AVAILABLE IN 42 COUNTRIES","🔒 ZERO DATA BREACH RECORD","📋 SOC 2 TYPE II AUDITED","🔐 256-BIT ENCRYPTION ACTIVE","✅ ISO 27001 CERTIFIED","🛡️ GDPR COMPLIANT","⚡ 99.97% UPTIME SLA","🌍 AVAILABLE IN 42 COUNTRIES","🔒 ZERO DATA BREACH RECORD","📋 SOC 2 TYPE II AUDITED"];
const features = [
  {ico:"🛡️",title:"Military-Grade Encryption",desc:"All personal data is encrypted with AES-256 before storage. Even our engineers cannot access your raw information.",tag:"AES-256 / TLS 1.3"},
  {ico:"🪪",title:"Verified Identity Profiles",desc:"Each user receives a tamper-proof digital identity profile linked to a unique Security Transaction Reference.",tag:"STR™ TECHNOLOGY"},
  {ico:"⚡",title:"Instant Verification",desc:"Share your STR reference instead of raw data. Recipients verify in under 3 seconds without exposing your details.",tag:"< 3 SEC VERIFY"},
  {ico:"📋",title:"Full Audit Trail",desc:"Every access request, verification, and transaction is logged with timestamp, IP, and device fingerprint.",tag:"IMMUTABLE LOGS"},
  {ico:"🔔",title:"Real-Time Alerts",desc:"Get notified instantly whenever your reference is accessed or used in any transaction — SMS, email, or push.",tag:"LIVE MONITORING"},
  {ico:"🌍",title:"Global Compliance",desc:"Built to meet GDPR, CCPA, PDPA and other regional data protection laws. Used across 42 countries worldwide.",tag:"MULTI-JURISDICTION"},
];
const steps = [
  {num:"01",icon:"📝",title:"Register",desc:"Create your verified profile with name, contact details, and identity documents."},
  {num:"02",icon:"✅",title:"Verify",desc:"Complete KYC verification via government ID or biometric scan."},
  {num:"03",icon:"🔑",title:"Get STR",desc:"Receive your unique Security Transaction Reference number."},
  {num:"04",icon:"🚀",title:"Transact",desc:"Share your STR for any transaction. Recipients verify securely without seeing your raw data."},
];
const testimonials = [
  {quote:"SecureID has completely transformed how we onboard clients. Verification that used to take days now happens in seconds.",name:"Amara Osei",role:"Head of Compliance, FinCore Ltd",init:"AO"},
  {quote:"I no longer worry about sharing personal details online. My STR reference keeps my information safe at every step.",name:"Fatima Al-Hassan",role:"Freelance Consultant",init:"FA"},
  {quote:"The audit trail feature alone justified the enterprise subscription. Full visibility into who accessed what, when.",name:"David Mensah",role:"IT Director, NovaTech Africa",init:"DM"},
];
const plans = [
  {name:"Personal",price:"$9",period:"/month",features:["1 verified profile","STR reference number","50 verifications/mo","Email alerts","Basic audit log"],btn:"outline",featured:false},
  {name:"Business",price:"$49",period:"/month",badge:"MOST POPULAR",features:["5 verified profiles","Bulk STR issuance","Unlimited verifications","SMS + Email alerts","Full audit trail","API access","Priority support"],btn:"fill",featured:true},
  {name:"Enterprise",price:"Custom",period:"contact us",features:["Unlimited profiles","White-label option","SLA guarantee","Dedicated support","Custom compliance","SIEM integration"],btn:"outline",featured:false},
];
const navLinks = [
  {id:"how-it-works",label:"How It Works"},
  {id:"features",label:"Features"},
  {id:"pricing",label:"Pricing"},
  {id:"demo",label:"Demo"},
  {id:"contact",label:"Contact"},
];

// ── AI ADVISOR ────────────────────────────────────────────────────────────────
function AIAdvisor() {
  const [open, setOpen] = useState(false);
  const [messages, setMessages] = useState([{role:"bot",text:"Hi! I'm ARIA, SecureID's AI Security Advisor 🛡️\n\nAsk me anything about how SecureID protects your data, how STR references work, pricing, compliance, or anything else!"}]);
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const [showNotif, setShowNotif] = useState(true);
  const endRef = useRef(null);
  const SYSTEM = `You are ARIA, an AI Security Advisor for SecureID — a personal data vault and secure transaction reference platform. Be helpful, concise, and professional but warm. Key facts: AES-256 encryption, TLS 1.3, ISO 27001, SOC 2 Type II, GDPR/CCPA/PDPA compliant. STR verification < 3 seconds. 99.97% uptime. 42 countries. Pricing: Personal $9/mo, Business $49/mo, Enterprise custom. 30-day money-back. Full audit trail, real-time alerts. Keep answers under 80 words.`;
  const chips = ["How does STR work?","Is my data safe?","Business pricing?","GDPR compliant?"];

  const sendMsg = async (text) => {
    const q = (text || input).trim();
    if (!q || loading) return;
    setInput(""); setShowNotif(false);
    setMessages(m => [...m, {role:"user",text:q}]);
    setLoading(true);
    try {
      const history = messages.map(m => ({role:m.role==="bot"?"assistant":"user",content:m.text}));
      history.push({role:"user",content:q});
      const res = await fetch("https://api.anthropic.com/v1/messages",{method:"POST",headers:{"Content-Type":"application/json"},body:JSON.stringify({model:"claude-sonnet-4-20250514",max_tokens:1000,system:SYSTEM,messages:history})});
      const data = await res.json();
      setMessages(m => [...m, {role:"bot",text:data.content?.map(b=>b.text||"").join("")||"Sorry, try again."}]);
    } catch { setMessages(m => [...m, {role:"bot",text:"Connection issue. Please try again."}]); }
    setLoading(false);
    setTimeout(()=>endRef.current?.scrollIntoView({behavior:"smooth"}),50);
  };

  return (
    <>
      <style>{AI_STYLE}</style>
      {open && (
        <div className="ai-panel">
          <div className="ai-header">
            <div className="ai-header-left">
              <div className="ai-avatar">🛡️</div>
              <div><div className="ai-name">ARIA — AI ADVISOR</div><div className="ai-status"><div className="ai-status-dot"/>ONLINE & READY</div></div>
            </div>
            <button className="ai-close" onClick={()=>setOpen(false)}>✕</button>
          </div>
          <div className="ai-messages">
            {messages.map((m,i)=>(
              <div className={`ai-msg ${m.role}`} key={i}>
                <div className="ai-msg-ico">{m.role==="bot"?"🛡️":"👤"}</div>
                <div className="ai-msg-bubble" style={{whiteSpace:"pre-wrap"}}>{m.text}</div>
              </div>
            ))}
            {loading && <div className="ai-msg bot"><div className="ai-msg-ico">🛡️</div><div className="ai-msg-bubble ai-typing"><span/><span/><span/></div></div>}
            <div ref={endRef}/>
          </div>
          {!loading && messages.length < 3 && <div className="ai-chips">{chips.map((c,i)=><div className="ai-chip" key={i} onClick={()=>sendMsg(c)}>{c}</div>)}</div>}
          <div className="ai-input-row">
            <input className="ai-input" placeholder="Ask about SecureID..." value={input} onChange={e=>setInput(e.target.value)} onKeyDown={e=>e.key==="Enter"&&sendMsg()}/>
            <button className="ai-send" onClick={()=>sendMsg()} disabled={loading||!input.trim()}>➤</button>
          </div>
        </div>
      )}
      <button className="ai-fab" onClick={()=>{setOpen(o=>!o);setShowNotif(false);}}>
        {open?"✕":"🛡️"}
        {showNotif&&!open&&<div className="notif"/>}
      </button>
    </>
  );
}

// ── REGISTRATION MODAL ────────────────────────────────────────────────────────
function RegistrationModal({onClose, defaultPlan="personal"}) {
  const [step, setStep] = useState(1);
  const [str, setStr] = useState("");
  const [strCopied, setStrCopied] = useState(false);
  const [submitting, setSubmitting] = useState(false);
  const [form, setForm] = useState({name:"",email:"",phone:"",password:"",confirm:"",plan:defaultPlan,terms:false});
  const [errs, setErrs] = useState({});

  const genSTR = () => {
    const c="ABCDEFGHJKLMNPQRSTUVWXYZ23456789";
    const seg=()=>Array.from({length:4},()=>c[Math.floor(Math.random()*c.length)]).join("");
    return `STR-${seg()}-${seg()}-${seg()}`;
  };
  const setField=(k,v)=>setForm(f=>({...f,[k]:v}));
  const clearErr=(k)=>setErrs(e=>({...e,[k]:""}));

  const validate = () => {
    const e={};
    if(step===1){
      if(!form.name.trim()) e.name="FULL NAME REQUIRED";
      if(!/^[^@]+@[^@]+\.[^@]+$/.test(form.email)) e.email="VALID EMAIL REQUIRED";
      if(!/^\+?[\d\s\-]{7,}$/.test(form.phone)) e.phone="VALID PHONE REQUIRED";
    }
    if(step===2){
      if(form.password.length<8) e.password="MIN 8 CHARACTERS";
      if(form.password!==form.confirm) e.confirm="PASSWORDS DO NOT MATCH";
      if(!form.terms) e.terms="PLEASE ACCEPT TERMS TO CONTINUE";
    }
    setErrs(e);
    return Object.keys(e).length===0;
  };

  const nextStep = async () => {
    if(!validate()) return;
    if(step===3){
      setSubmitting(true);
      try {
        const res=await fetch("/api/register",{method:"POST",headers:{"Content-Type":"application/json"},body:JSON.stringify({name:form.name,email:form.email,phone:form.phone,plan:form.plan})});
        const data=await res.json();
        setStr(data.str||genSTR());
      } catch { setStr(genSTR()); }
      setSubmitting(false);
      setStep(4);
    } else setStep(s=>s+1);
  };

  const copyStr=()=>{navigator.clipboard.writeText(str).then(()=>{setStrCopied(true);setTimeout(()=>setStrCopied(false),2000)});};
  const planOptions=[{id:"personal",name:"Personal",price:"$9 / month",pop:null},{id:"business",name:"Business",price:"$49 / month",pop:"MOST POPULAR"},{id:"enterprise",name:"Enterprise",price:"Custom pricing",pop:null}];
  const stepLabels=["PERSONAL INFO","SECURITY SETUP","CHOOSE PLAN","YOUR STR"];

  return (
    <div className="reg-overlay" onClick={e=>e.target===e.currentTarget&&onClose()}>
      <style>{REG_STYLE}</style>
      <div className="reg-modal">
        <div className="reg-header">
          <div className="reg-logo">🛡️ SECUREID — REGISTER</div>
          <button className="reg-x" onClick={onClose}>✕</button>
        </div>
        {step<4&&(
          <div className="reg-progress">
            <div className="reg-bars">{[1,2,3,4].map(n=><div key={n} className={`reg-bar${n<step?" done":n===step?" active":""}`}/>)}</div>
            <div className="reg-step-lbl">STEP {step} OF 3 — {stepLabels[step-1]}</div>
          </div>
        )}
        <div className="reg-body">
          {step===1&&<>
            <div className="reg-title">Create Your Account</div>
            <div className="reg-sub">// ENTER YOUR PERSONAL DETAILS BELOW</div>
            {[{k:"name",label:"FULL NAME",type:"text",ph:"e.g. Amara Osei"},{k:"email",label:"EMAIL ADDRESS",type:"email",ph:"you@example.com"},{k:"phone",label:"PHONE NUMBER",type:"tel",ph:"+1 555 000 0000"}].map(f=>(
              <div className="reg-field" key={f.k}>
                <label>{f.label}</label>
                <input className={`reg-input${errs[f.k]?" err":""}`} type={f.type} placeholder={f.ph} value={form[f.k]} onChange={e=>{setField(f.k,e.target.value);clearErr(f.k);}}/>
                {errs[f.k]&&<div className="reg-err">⚠ {errs[f.k]}</div>}
              </div>
            ))}
            <div className="reg-actions"><button className="reg-next" onClick={nextStep}>CONTINUE →</button></div>
          </>}
          {step===2&&<>
            <div className="reg-title">Set Your Password</div>
            <div className="reg-sub">// MINIMUM 8 CHARACTERS — STORE IT SAFELY</div>
            {[{k:"password",label:"PASSWORD"},{k:"confirm",label:"CONFIRM PASSWORD"}].map(f=>(
              <div className="reg-field" key={f.k}>
                <label>{f.label}</label>
                <input className={`reg-input${errs[f.k]?" err":""}`} type="password" placeholder="••••••••" value={form[f.k]} onChange={e=>{setField(f.k,e.target.value);clearErr(f.k);}}/>
                {errs[f.k]&&<div className="reg-err">⚠ {errs[f.k]}</div>}
              </div>
            ))}
            <div className="reg-field">
              <div className="reg-chk-row">
                <input type="checkbox" id="terms" className="reg-chk" checked={form.terms} onChange={e=>{setField("terms",e.target.checked);clearErr("terms");}}/>
                <label htmlFor="terms" className="reg-chk-lbl">I agree to SecureID's <a href="#">Terms of Service</a> and <a href="#">Privacy Policy</a>. I understand my data is encrypted with AES-256.</label>
              </div>
              {errs.terms&&<div className="reg-err" style={{marginTop:6}}>⚠ {errs.terms}</div>}
            </div>
            <div className="reg-actions"><button className="reg-back" onClick={()=>setStep(1)}>← BACK</button><button className="reg-next" onClick={nextStep}>CONTINUE →</button></div>
          </>}
          {step===3&&<>
            <div className="reg-title">Choose Your Plan</div>
            <div className="reg-sub">// ALL PLANS INCLUDE 30-DAY MONEY-BACK GUARANTEE</div>
            <div className="reg-plans">
              {planOptions.map(p=>(
                <div key={p.id} className={`reg-plan${form.plan===p.id?" sel":""}`} onClick={()=>setField("plan",p.id)}>
                  <div className="reg-radio">{form.plan===p.id&&<div className="reg-dot"/>}</div>
                  <div><div className="reg-pname">{p.name}</div><div className="reg-pprice">{p.price}</div></div>
                  {p.pop&&<div className="reg-pop">{p.pop}</div>}
                </div>
              ))}
            </div>
            <div className="reg-actions"><button className="reg-back" onClick={()=>setStep(2)}>← BACK</button><button className="reg-next" onClick={nextStep} disabled={submitting}>{submitting?"CREATING...":"CREATE ACCOUNT →"}</button></div>
          </>}
          {step===4&&(
            <div className="reg-success">
              <span className="reg-sico">🎉</span>
              <div className="reg-stitle">ACCOUNT CREATED!</div>
              <p style={{fontFamily:"'Share Tech Mono',monospace",fontSize:11,color:"#7090a8",letterSpacing:1}}>Welcome, {form.name.split(" ")[0]}! Your Security Transaction Reference is ready.</p>
              <div className="reg-str-box">
                <div className="reg-str-lbl">YOUR SECURITY TRANSACTION REFERENCE (STR)</div>
                <div className="reg-str-val">{str}</div>
                <button className="reg-str-copy" onClick={copyStr}>{strCopied?"✓ COPIED!":"📋 COPY STR"}</button>
              </div>
              <p className="reg-snote">This is your unique identifier for all transactions.<br/>A confirmation email has been sent to <span style={{color:"var(--gold)"}}>{form.email}</span>.<br/>Store your STR safely — treat it like a password.</p>
              <button className="reg-next" style={{marginTop:20,width:"100%"}} onClick={onClose}>DONE →</button>
            </div>
          )}
        </div>
      </div>
    </div>
  );
}

// ── CONTACT FORM ──────────────────────────────────────────────────────────────
function ContactForm() {
  const [form, setForm] = useState({name:"",email:"",subject:"general",message:""});
  const [errs, setErrs] = useState({});
  const [status, setStatus] = useState("idle"); // idle | sending | success

  const setF = (k,v) => setForm(f=>({...f,[k]:v}));
  const clrE = (k) => setErrs(e=>({...e,[k]:""}));

  const validate = () => {
    const e = {};
    if (!form.name.trim())    e.name    = "NAME IS REQUIRED";
    if (!/^[^@]+@[^@]+\.[^@]+$/.test(form.email)) e.email = "VALID EMAIL REQUIRED";
    if (!form.message.trim()) e.message = "MESSAGE IS REQUIRED";
    setErrs(e);
    return Object.keys(e).length === 0;
  };

  const handleSubmit = async () => {
    if (!validate()) return;
    setStatus("sending");
    // Simulate API call — replace with fetch("/api/contact", {...}) in production
    await new Promise(r => setTimeout(r, 1600));
    setStatus("success");
  };

  const reset = () => { setForm({name:"",email:"",subject:"general",message:""}); setErrs({}); setStatus("idle"); };

  const subjects = [
    {val:"general",  label:"General Enquiry"},
    {val:"support",  label:"Technical Support"},
    {val:"sales",    label:"Sales & Pricing"},
    {val:"security", label:"Security Concern"},
    {val:"partner",  label:"Partnership"},
  ];

  return (
    <div className="contact-form-card">
      <div className="contact-form-header">
        <span>✉️</span>
        <div className="contact-form-title">SEND US A MESSAGE</div>
        <div style={{marginLeft:"auto",display:"flex",alignItems:"center",gap:6,fontFamily:"'Share Tech Mono',monospace",fontSize:9,color:"#00e676"}}>
          <div style={{width:5,height:5,borderRadius:"50%",background:"#00e676",animation:"blink 1.2s infinite"}}/>
          RESPONSE &lt; 24H
        </div>
      </div>
      <div className="contact-form-body">
        {status === "success" ? (
          <div className="cf-success">
            <span className="cf-success-ico">✅</span>
            <div className="cf-success-title">MESSAGE SENT!</div>
            <p className="cf-success-note">
              Thanks, {form.name.split(" ")[0]}! We've received your message<br/>
              and will reply to <span style={{color:"var(--gold)"}}>{form.email}</span> within 24 hours.
            </p>
            <button className="cf-reset" onClick={reset}>SEND ANOTHER →</button>
          </div>
        ) : (
          <>
            <div className="cf-row">
              <div className="cf-field">
                <label>FULL NAME</label>
                <input className={`cf-input${errs.name?" err":""}`} placeholder="e.g. Amara Osei" value={form.name} onChange={e=>{setF("name",e.target.value);clrE("name");}}/>
                {errs.name && <div className="cf-err">⚠ {errs.name}</div>}
              </div>
              <div className="cf-field">
                <label>EMAIL ADDRESS</label>
                <input className={`cf-input${errs.email?" err":""}`} type="email" placeholder="you@example.com" value={form.email} onChange={e=>{setF("email",e.target.value);clrE("email");}}/>
                {errs.email && <div className="cf-err">⚠ {errs.email}</div>}
              </div>
            </div>
            <div className="cf-field">
              <label>SUBJECT</label>
              <select className="cf-input cf-select" value={form.subject} onChange={e=>setF("subject",e.target.value)}>
                {subjects.map(s=><option key={s.val} value={s.val}>{s.label}</option>)}
              </select>
            </div>
            <div className="cf-field">
              <label>MESSAGE</label>
              <textarea className={`cf-input cf-textarea${errs.message?" err":""}`} placeholder="Tell us how we can help you..." value={form.message} onChange={e=>{setF("message",e.target.value);clrE("message");}}/>
              {errs.message && <div className="cf-err">⚠ {errs.message}</div>}
            </div>
            <button className="cf-submit" onClick={handleSubmit} disabled={status==="sending"}>
              {status==="sending" ? "SENDING..." : "SEND MESSAGE →"}
            </button>
          </>
        )}
      </div>
    </div>
  );
}


export default function App() {
  const [demoVerified, setDemoVerified] = useState(false);
  const [demoLoading, setDemoLoading] = useState(false);
  const [showReg, setShowReg] = useState(false);
  const [regPlan, setRegPlan] = useState("personal");
  const [activeLink, setActiveLink] = useState("");
  const refNum = "STR-X7K2-9PQR-4MNW";

  const openReg = (plan="personal") => { setRegPlan(plan); setShowReg(true); };
  const handleDemoVerify = () => { setDemoLoading(true); setTimeout(()=>{setDemoLoading(false);setDemoVerified(true);},1800); };
  const handleDemoReset = () => { setDemoVerified(false); setDemoLoading(false); };

  const goToSection = (id) => {
    setActiveLink(id);
    const el = document.getElementById(id);
    if (el) el.scrollIntoView({ behavior:"smooth", block:"start" });
  };

  return (
    <>
      <style>{style}</style>
      <div className="scanline-overlay"/>
      {showReg && <RegistrationModal onClose={()=>setShowReg(false)} defaultPlan={regPlan}/>}

      {/* NAVBAR */}
      <nav className="navbar">
        <div className="nav-logo" onClick={()=>window.scrollTo({top:0,behavior:"smooth"})}>
          <span>🛡️</span>
          <div><div>SECUREID</div><div style={{fontSize:9,letterSpacing:2,color:"#4a6080"}}>PERSONAL DATA VAULT</div></div>
        </div>
        <div className="nav-links">
          {navLinks.map(l=>(
            <a key={l.id} className={activeLink===l.id?"active":""} onClick={()=>goToSection(l.id)}>{l.label}</a>
          ))}
        </div>
        <button className="nav-cta" onClick={()=>openReg()}>Get Started Free</button>
      </nav>

      {/* TICKER */}
      <div className="ticker-bar">
        <div className="ticker-label">🔴 LIVE</div>
        <div className="ticker-content">{tickerItems.map((t,i)=><span key={i} className="ticker-item">{t}<span className="ticker-sep">◆</span></span>)}</div>
      </div>

      {/* HERO */}
      <section className="hero">
        <div className="hero-grid"/><div className="hero-glow"/><div className="hero-glow2"/>
        <div className="hero-inner">
          <div>
            <div className="hero-badge"><div className="hero-badge-dot"/><span>TRUSTED BY 200,000+ USERS WORLDWIDE</span></div>
            <h1 className="hero-h1">YOUR DATA.<br/><span className="gold">YOUR CONTROL.</span><br/><span className="cyan">ZERO EXPOSURE.</span></h1>
            <p className="hero-sub">The world's most secure personal contact details service. Share a single reference number — never your raw data — for every online transaction, contract, and verification.</p>
            <div className="hero-actions">
              <button className="btn-primary" onClick={()=>openReg()}>🔐 Create Free Account</button>
              <button className="btn-outline" onClick={()=>goToSection("demo")}>▶ Watch Demo</button>
            </div>
          </div>
          <div className="hero-visual">
            <div className="shield-wrap">
              <div className="pulse-ring"/><div className="pulse-ring pulse-ring2"/>
              <div className="shield-outer">
                <div className="shield-inner">
                  <div className="shield-icon">🛡️</div>
                  <div className="shield-ref">{refNum}</div>
                  <div className="shield-status"><div className="status-dot"/>IDENTITY VERIFIED</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      {/* STATS */}
      <div className="stats-strip">
        <div className="stats-inner">
          {[{num:"200",unit:"K+",label:"REGISTERED USERS"},{num:"4.2",unit:"M",label:"TRANSACTIONS SECURED"},{num:"99.97",unit:"%",label:"UPTIME GUARANTEED"},{num:"42",unit:"+",label:"COUNTRIES SERVED"}].map((s,i)=>(
            <div className="stat-item" key={i} style={{animationDelay:`${i*.1}s`}}>
              <div><span className="stat-num">{s.num}</span><span className="stat-unit">{s.unit}</span></div>
              <div className="stat-label">{s.label}</div>
            </div>
          ))}
        </div>
      </div>

      {/* HOW IT WORKS */}
      <section className="section" id="how-it-works">
        <div className="section-inner">
          <div className="section-eyebrow">// HOW IT WORKS</div>
          <h2 className="section-title">FOUR STEPS TO <span className="gold">TOTAL SECURITY</span></h2>
          <p className="section-desc">From sign-up to your first secure transaction in under 5 minutes.</p>
          <div className="steps-grid">
            {steps.map((s,i)=>(
              <div className="step-card" key={i}>
                <div className="step-num">{s.num}</div>
                <div className="step-icon">{s.icon}</div>
                <div className="step-title">{s.title}</div>
                <div className="step-desc">{s.desc}</div>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* FEATURES */}
      <section className="features-section" id="features">
        <div className="section-inner">
          <div className="section-eyebrow">// PLATFORM FEATURES</div>
          <h2 className="section-title">BUILT FOR <span className="gold">UNCOMPROMISING</span> SECURITY</h2>
          <p className="section-desc">Every feature designed around one principle: your personal information stays private, always.</p>
          <div className="features-grid">
            {features.map((f,i)=>(
              <div className="feature-card" key={i}>
                <div className="feature-ico">{f.ico}</div>
                <div className="feature-title">{f.title}</div>
                <div className="feature-desc">{f.desc}</div>
                <div className="feature-tag">{f.tag}</div>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* DEMO */}
      <section className="demo-section" id="demo">
        <div className="section-inner" style={{textAlign:"center"}}>
          <div className="section-eyebrow">// LIVE DEMO</div>
          <h2 className="section-title" style={{margin:"0 auto 12px"}}>SEE IT <span className="gold">IN ACTION</span></h2>
          <p className="section-desc" style={{margin:"0 auto 48px"}}>Try a simulated secure transaction verification — no account needed.</p>
          <div className="demo-card">
            <div className="demo-header">
              <div className="demo-header-title">🔐 SECURE TRANSACTION PORTAL — DEMO MODE</div>
              <div className="demo-dots"><div className="demo-dot" style={{background:"#ff5f56"}}/><div className="demo-dot" style={{background:"#ffbd2e"}}/><div className="demo-dot" style={{background:"#27c93f"}}/></div>
            </div>
            <div className="demo-body">
              <div className="demo-row">
                <div className="demo-field"><label>TRANSACTION TYPE</label><div className="value">Contract Signing</div></div>
                <div className="demo-field"><label>DATE / TIME</label><div className="value">21 MAY 2026 — 14:32 UTC</div></div>
              </div>
              <div className="demo-field"><label>SECURITY TRANSACTION REFERENCE (STR)</label><div className="value ref">{refNum}</div></div>
              <div className="demo-row">
                <div className="demo-field"><label>IDENTITY STATUS</label><div className="value verified">{demoVerified?"✅ VERIFIED & AUTHENTICATED":demoLoading?"⏳ VERIFYING...":"⏸ PENDING VERIFICATION"}</div></div>
                <div className="demo-field"><label>DATA EXPOSED</label><div className="value" style={{color:"#00e676"}}>NONE — STR ONLY</div></div>
              </div>
              {demoVerified&&<div className="demo-badge"><div className="demo-badge-dot"/><span>IDENTITY CONFIRMED — TRANSACTION AUTHORISED — AUDIT LOG UPDATED</span></div>}
              <div className="demo-actions">
                <button className="demo-btn primary" onClick={handleDemoVerify} disabled={demoLoading||demoVerified}>{demoLoading?"VERIFYING...":demoVerified?"VERIFIED ✓":"VERIFY REFERENCE"}</button>
                <button className="demo-btn secondary" onClick={handleDemoReset}>RESET DEMO</button>
              </div>
            </div>
          </div>
        </div>
      </section>

      {/* TESTIMONIALS */}
      <section className="testimonials">
        <div className="section-inner">
          <div className="section-eyebrow">// CLIENT TESTIMONIALS</div>
          <h2 className="section-title">TRUSTED BY <span className="gold">PROFESSIONALS</span></h2>
          <p className="section-desc" style={{marginBottom:48}}>From individual users to enterprise compliance teams.</p>
          <div className="test-grid">
            {testimonials.map((t,i)=>(
              <div className="test-card" key={i}>
                <div className="test-stars">★★★★★</div>
                <p className="test-quote">"{t.quote}"</p>
                <div className="test-author">
                  <div className="test-avatar">{t.init}</div>
                  <div><div className="test-name">{t.name}</div><div className="test-role">{t.role}</div></div>
                </div>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* PRICING */}
      <section className="pricing" id="pricing">
        <div className="section-inner" style={{textAlign:"center"}}>
          <div className="section-eyebrow">// PRICING PLANS</div>
          <h2 className="section-title">SECURE YOUR DATA <span className="gold">TODAY</span></h2>
          <p className="section-desc" style={{margin:"0 auto 52px"}}>No hidden fees. Cancel anytime. 30-day money-back guarantee.</p>
          <div className="pricing-grid">
            {plans.map((p,i)=>(
              <div className={`plan-card${p.featured?" featured":""}`} key={i}>
                {p.badge&&<div className="plan-badge">{p.badge}</div>}
                <div className="plan-name">{p.name}</div>
                <div className="plan-price">{p.price}</div>
                <div className="plan-period">{p.period}</div>
                <ul className="plan-features">{p.features.map((f,j)=><li key={j}><span className="check">✓</span>{f}</li>)}</ul>
                <button className={`plan-btn ${p.btn}`} onClick={()=>openReg(p.name.toLowerCase())}>Get {p.name}</button>
              </div>
            ))}
          </div>
        </div>
      </section>

      {/* CONTACT */}
      <section className="contact-section" id="contact">
        <div className="section-inner" style={{maxWidth:1000,margin:"0 auto"}}>
          <div className="section-eyebrow">// GET IN TOUCH</div>
          <h2 className="section-title">CONTACT <span className="gold">SECUREID</span></h2>
          <p className="section-desc" style={{marginBottom:48}}>Have a question, security concern, or want to explore enterprise pricing? We respond within 24 hours.</p>
          <div className="contact-grid">
            {/* LEFT — Info */}
            <div>
              {[
                {ico:"📧", label:"EMAIL US", val:"crishaternal490@gmail.com"},
                {ico:"📞", label:"CALL US", val:"+63 975 662 5139"},
                {ico:"🌍", label:"HEADQUARTERS", val:"Metro Manila, Philippines"},
                {ico:"⏰", label:"SUPPORT HOURS", val:"Mon–Fri, 9AM–6PM UTC"},
                {ico:"🔐", label:"SECURITY REPORTS", val:"crishaternal490@gmail.com"},
                {ico:"👤", label:"PLATFORM BUILDER", val:"Crisha Ternal — Admin"},
              ].map((item,i)=>(
                <div className="contact-info-item" key={i}>
                  <div className="contact-ico">{item.ico}</div>
                  <div>
                    <div className="contact-info-label">{item.label}</div>
                    <div className="contact-info-val">{item.val}</div>
                  </div>
                </div>
              ))}
              <div style={{marginTop:8,padding:"16px",background:"rgba(0,212,255,.05)",border:"1px solid var(--cyan-dim)",borderRadius:8}}>
                <div style={{fontFamily:"'Share Tech Mono',monospace",fontSize:10,color:"var(--cyan)",letterSpacing:2,marginBottom:6}}>// ENTERPRISE SUPPORT</div>
                <div style={{fontFamily:"'Rajdhani',sans-serif",fontSize:13,color:"var(--muted)",lineHeight:1.6}}>Enterprise clients get a dedicated account manager and 1-hour SLA response time. <span style={{color:"var(--gold)"}}>Contact sales to learn more.</span></div>
              </div>
            </div>
            {/* RIGHT — Form */}
            <ContactForm/>
          </div>
        </div>
      </section>

      {/* CTA */}
      <section className="cta-band">
        <h2 className="cta-title">STOP SHARING YOUR DATA.<br/><span className="gold">START SHARING YOUR REFERENCE.</span></h2>
        <p className="cta-sub">Join 200,000+ users who transact securely every day — without exposing a single personal detail.</p>
        <div className="cta-btns">
          <button className="btn-primary" style={{fontSize:18,padding:"16px 48px"}} onClick={()=>openReg()}>🔐 Create Free Account</button>
          <button className="btn-outline" style={{fontSize:18,padding:"16px 48px"}} onClick={()=>goToSection("contact")}>📞 Talk to Sales</button>
        </div>
        <div className="cta-trust">
          {["NO CREDIT CARD REQUIRED","30-DAY MONEY BACK","SETUP IN 5 MINUTES","CANCEL ANYTIME"].map((t,i)=>(
            <div className="trust-item" key={i}><span className="trust-icon">✓</span>{t}</div>
          ))}
        </div>
      </section>

      {/* FOOTER */}
      <footer id="contact">
        <div className="footer-logo">🛡️ SECUREID</div>
        <div className="footer-links">{["Privacy Policy","Terms of Service","Security","Contact","API Docs","Compliance"].map((l,i)=><a key={i}>{l}</a>)}</div>
        <div className="footer-legal">© 2026 SecureID Personal Data Vault. All rights reserved.<br/>This is a demonstration platform. Transaction references shown are for illustrative purposes only.</div>
      </footer>

      <AIAdvisor/>
    </>
  );
}
