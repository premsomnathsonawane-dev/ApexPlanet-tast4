# ApexPlanet-tast4

Author- Prem Somnath Sonawane

TASK 4:
Security Enhancements
Secure the application against common web vulnerabilities.
Implement form validation and user roles.
Use PDO or MySQLi to implement prepared statements for all database queries to prevent SQL
injection attacks.
Prepared Statements :
Objective :
Add server-side validation for all forms to ensure data integrity.
Implement client-side validation for an enhanced user experience.
Form Validation :
Extend the user table to include roles (e.g., admin, editor).
Implement role-based access control for different parts of the application.
User Roles and Permissions :
A more secure application with prepared statements, form validation, and user roles.
Documentation on the security measures implemented.



CODE Execution:

import React, { useState } from "react";
import { 
  ShieldCheck, 
  Database, 
  Key, 
  Lock, 
  User, 
  Mail, 
  FileCode, 
  AlertTriangle, 
  CheckCircle2, 
  RefreshCw, 
  Play, 
  Terminal, 
  ArrowRight, 
  LockOpen, 
  BookOpen, 
  Eye, 
  EyeOff, 
  Layers, 
  Fingerprint, 
  Plus, 
  AlertOctagon,
  FileText,
  BadgeAlert,
  HardDrive,
  Check
} from "lucide-react";
import { PHP_CODEBASE, SECURITY_REPORT_MD, CodeFile } from "./data";
import { motion, AnimatePresence } from "motion/react";

// Types for Simulator State
interface SimulatedUser {
  id: number;
  username: string;
  email: string;
  passwordHash: string;
  role: "admin" | "editor" | "user";
  createdAt: string;
}

export default function App() {
  const [activeTab, setActiveTab] = useState<"playground" | "rbac" | "codebase" | "docs">("playground");
  const [selectedFileIndex, setSelectedFileIndex] = useState<number>(0);
  const [hoveredAnnotation, setHoveredAnnotation] = useState<number | null>(null);

  // Playground Form State
  const [username, setUsername] = useState("johndoe");
  const [email, setEmail] = useState("john@secure.com");
  const [password, setPassword] = useState("P@ssword123!");
  const [csrfOption, setCsrfOption] = useState<"valid" | "invalid" | "missing">("valid");
  const [showPassword, setShowPassword] = useState(false);

  // Playground Log & DB State
  const [simulatedUsers, setSimulatedUsers] = useState<SimulatedUser[]>([
    {
      id: 1,
      username: "system_admin",
      email: "admin@securesubsystem.local",
      passwordHash: "$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi",
      role: "admin",
      createdAt: "2026-07-03 10:15:30"
    }
  ]);

  const [sandboxLog, setSandboxLog] = useState<{
    timestamp: string;
    level: "info" | "success" | "warning" | "error";
    module: string;
    message: string;
    details?: string;
  }[]>([
    {
      timestamp: "23:55:39",
      level: "info",
      module: "SYSTEM",
      message: "PHP Security Subsystem sandbox initialized.",
      details: "Database.php loaded with strict PDO parameters. Ready for transaction audits."
    }
  ]);

  // RBAC Simulator State
  const [selectedRbacRole, setSelectedRbacRole] = useState<"guest" | "user" | "editor" | "admin">("guest");
  const [dashboardAccessResult, setDashboardAccessResult] = useState<{
    status: "idle" | "granted" | "forbidden";
    log: string;
  }>({ status: "idle", log: "" });

  // Load Preset Handler
  const applyPreset = (preset: "legit" | "sqli" | "xss" | "weak") => {
    switch (preset) {
      case "legit":
        setUsername("securesmith");
        setEmail("smith@defenders.org");
        setPassword("ShieldUp@2026!");
        setCsrfOption("valid");
        addLog("PRESET", "Loaded legitimate registration parameters.", "Alphanumeric username, structured RFC email, strong entropy password.", "info");
        break;
      case "sqli":
        setUsername("admin' OR '1'='1");
        setEmail("attacker@inject.com");
        setPassword("InjectPass99!");
        setCsrfOption("valid");
        addLog("PRESET", "Loaded dangerous SQL Injection payload.", "Username contains logic-breaking escape quote: admin' OR '1'='1", "warning");
        break;
      case "xss":
        setUsername("<script>alert('XSS')</script>");
        setEmail("malicious@script.com");
        setPassword("SafeScript@1!");
        setCsrfOption("valid");
        addLog("PRESET", "Loaded dangerous Cross-Site Scripting (XSS) payload.", "Username contains active script block: <script>...", "warning");
        break;
      case "weak":
        setUsername("weakuser");
        setEmail("weak@user.com");
        setPassword("12345");
        setCsrfOption("valid");
        addLog("PRESET", "Loaded weak password parameters.", "Short password lacking capital letters, numerals, and special characters.", "warning");
        break;
    }
  };

  const addLog = (module: string, message: string, details?: string, level: "info" | "success" | "warning" | "error" = "info") => {
    const time = new Date().toLocaleTimeString();
    setSandboxLog(prev => [
      { timestamp: time, level, module, message, details },
      ...prev
    ]);
  };

  // Run Simulated Register Validation
  const handleSimulatedRegister = (e: React.FormEvent) => {
    e.preventDefault();
    addLog("SUBMIT", "Dispatching registration form payload to ValidationService.php...", `Data size: ${username.length + email.length + password.length} bytes`, "info");

    // Phase 1: Client-Side Validation Simulation
    addLog("CLIENT-JS", "Constraint Validation API verifying format correctness...", "", "info");
    
    // Check CSRF
    const csrfTokenValue = csrfOption === "valid" ? "6c78a3a0e1b9c2401f8e..." : csrfOption === "invalid" ? "EXPIRED_OR_SPUFE_TOKEN" : "";
    if (csrfOption === "missing" || csrfOption === "invalid") {
      addLog("SERVER-PHP", "CRITICAL ERROR: CSRF Validation Failed in ValidationService::validateCsrfToken()!", "Token mismatch or token was completely empty. Terminating thread.", "error");
      addLog("DATABASE", "SECURE GUARD: Transaction aborted. Zero database read/write executed.", "Prepared SQL statements were not compiled to protect CPU resources.", "warning");
      return;
    }

    // Client Validation
    const clientUsernameValid = /^[a-zA-Z0-9]{3,20}$/.test(username);
    const clientEmailValid = /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(email);
    const clientPasswordValid = username.length >= 3 && password.length >= 8 && /[A-Z]/.test(password) && /[a-z]/.test(password) && /[0-9]/.test(password) && /[!@#$%^&*()_+={}\[\]|\\:;"'<>,.?\/-]/.test(password);

    if (!clientUsernameValid || !clientEmailValid || !clientPasswordValid) {
      addLog("CLIENT-JS", "Validation BLOCKED: Incomplete or broken inputs captured.", "Browser prevented network dispatch.", "warning");
    } else {
      addLog("CLIENT-JS", "SUCCESS: Client inputs matched basic regular expression boundaries.", "Form dispatched to server.", "success");
    }

    // Phase 2: Server-Side Validation (ValidationService.php)
    addLog("SERVER-PHP", "ValidationService::validateRegistration() executing secure server-side filters...", "", "info");

    // Sanitization simulation (htmlspecialchars)
    const sanitizedUsername = username
      .replace(/&/g, "&amp;")
      .replace(/</g, "&lt;")
      .replace(/>/g, "&gt;")
      .replace(/"/g, "&quot;")
      .replace(/'/g, "&#039;");

    const isXssDetected = username !== sanitizedUsername;
    if (isXssDetected) {
      addLog("SERVER-PHP", "ATTACK INTERCEPTED: Malicious script tags detected in Username parameter!", `Original: "${username}" -> Sanitized to safe HTML entities: "${sanitizedUsername}"`, "warning");
    } else {
      addLog("SERVER-PHP", "Sanitization successfully processed Username parameter without adjustments.", `Data: "${sanitizedUsername}"`, "success");
    }

    // Server validate Username Regex
    const serverUsernameValid = /^[a-zA-Z0-9]{3,20}$/.test(username);
    if (!serverUsernameValid) {
      addLog("SERVER-PHP", "ERROR: Server-side Username constraint failed validation regex (/^[a-zA-Z0-9]{3,20}$/).", "Rejecting registration. Username must be strictly alphanumeric.", "error");
      return;
    }

    // Server validate Email
    const serverEmailValid = email.includes("@") && email.length > 5; // Simplified server validation
    if (!serverEmailValid) {
      addLog("SERVER-PHP", "ERROR: Server-side Email validation failed (filter_var with FILTER_VALIDATE_EMAIL).", "Please enter a valid format.", "error");
      return;
    }

    // Server validate Password Complexity
    const serverPasswordValid = password.length >= 8 && /[A-Z]/.test(password) && /[a-z]/.test(password) && /[0-9]/.test(password) && /[!@#$%^&*()_+={}\[\]|\\:;"'<>,.?\/-]/.test(password);
    if (!serverPasswordValid) {
      addLog("SERVER-PHP", "ERROR: Server-side Password complexity requirements unmet.", "Requires minimum 8 characters with upper, lower, numbers, and symbols.", "error");
      return;
    }

    addLog("SERVER-PHP", "SUCCESS: All inputs passed strict sanitization and regular expression evaluations.", "Handing off validated parameters to UserRepository.", "success");

    // Phase 3: Database & Hashing Layer (UserRepository.php)
    addLog("USER-REPO", "UserRepository::create() invoked.", "Initiating cryptographic hashing routines.", "info");

    // Compute Simulated Bcrypt Hash
    const salt = "$2y$10$" + Array.from({length: 22}, () => "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789./"[Math.floor(Math.random() * 64)]).join("");
    const simulatedBcryptHash = salt + Array.from({length: 31}, () => "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789./"[Math.floor(Math.random() * 64)]).join("");
    
    addLog("CRYPTOGRAPHY", "password_hash($password, PASSWORD_DEFAULT) computed using standard blowfish/bcrypt cost 10.", `Generated Hash: "${simulatedBcryptHash}"`, "success");

    addLog("DATABASE", "Compiling SQL statement via PDO prepare(): INSERT INTO users (username, email, password_hash) VALUES (:username, :email, :password_hash)", "Parameters separated from instruction channel. EMULATE_PREPARES disabled.", "info");
    addLog("DATABASE", "Executing prepared statement with securely bound variables.", `Binding - :username: "${sanitizedUsername}", :email: "${email}", :password_hash: "${simulatedBcryptHash}"`, "success");

    // Insert into state list
    const newId = simulatedUsers.length + 1;
    const newUser: SimulatedUser = {
      id: newId,
      username: sanitizedUsername,
      email: email,
      passwordHash: simulatedBcryptHash,
      role: "user",
      createdAt: new Date().toISOString().replace('T', ' ').substring(0, 19)
    };

    setSimulatedUsers(prev => [...prev, newUser]);
    addLog("DATABASE", `SUCCESS: Row successfully committed to 'users' table. Generated Insert ID: ${newId}`, "", "success");
    addLog("SYSTEM", "Registration pipeline completed safely and securely.", "No SQL execution vulnerabilities were present.", "success");
  };

  // RBAC Authentication Simulation
  const handleRbacDashboardRequest = () => {
    const timeStr = new Date().toLocaleTimeString();
    
    if (selectedRbacRole === "guest") {
      setDashboardAccessResult({
        status: "forbidden",
        log: `[SECURITY AUDIT] [${timeStr}] [IP: 192.168.1.52] - Access attempted by unauthenticated guest.\n[SYSTEM EVENT] - Terminated session thread, issued HTTP 403 Forbidden payload.`
      });
      addLog("RBAC", "Access attempted by unauthenticated guest. Blocked at SessionManager::requireRole().", "HTTP 403 Forbidden", "error");
    } else if (selectedRbacRole === "user") {
      setDashboardAccessResult({
        status: "forbidden",
        log: `[SECURITY AUDIT] [${timeStr}] [IP: 192.168.1.52] - Unauthorized authorization bypass attempt by User #4 (user) on admin dashboard.\n[SYSTEM EVENT] - Terminated session thread, issued HTTP 403 Forbidden payload.`
      });
      addLog("RBAC", "Standard 'user' blocked from restricted administrative resource.", "HTTP 403 Forbidden", "error");
    } else if (selectedRbacRole === "editor") {
      setDashboardAccessResult({
        status: "forbidden",
        log: `[SECURITY AUDIT] [${timeStr}] [IP: 192.168.1.52] - Unauthorized authorization bypass attempt by User #9 (editor) on admin dashboard.\n[SYSTEM EVENT] - Terminated session thread, issued HTTP 403 Forbidden payload.`
      });
      addLog("RBAC", "Editor blocked from restricted administrative resource.", "HTTP 403 Forbidden", "error");
    } else if (selectedRbacRole === "admin") {
      setDashboardAccessResult({
        status: "granted",
        log: `[SECURITY AUDIT] [${timeStr}] [IP: 192.168.1.52] - Authorized Access Granted for User #1 (admin) on admin dashboard.\n[HEADERS] - Set X-Frame-Options: DENY\n[HEADERS] - Set X-Content-Type-Options: nosniff`
      });
      addLog("RBAC", "Access GRANTED for 'admin' role. Restrict dashboard loaded safely.", "HTTP 200 OK", "success");
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-b from-[#fcfdfd] via-[#f8fafc] to-[#f1f5f9] text-slate-900 font-sans flex flex-col antialiased selection:bg-emerald-500/20 selection:text-emerald-900">
      {/* Executive Header */}
      <header className="bg-slate-950 text-white relative border-b border-slate-900 overflow-hidden shadow-[0_4px_20px_-4px_rgba(0,0,0,0.15)]">
        {/* Subtle decorative background glow */}
        <div className="absolute top-0 right-1/4 w-96 h-96 bg-emerald-500/5 rounded-full blur-3xl pointer-events-none" />
        <div className="absolute -bottom-10 left-1/3 w-80 h-80 bg-blue-500/5 rounded-full blur-3xl pointer-events-none" />

        <div className="max-w-7xl mx-auto py-7 px-6 md:px-8 relative z-10 flex flex-col lg:flex-row lg:items-center lg:justify-between gap-6">
          <div className="space-y-2.5">
            <div className="flex items-center gap-3">
              <span className="p-2.5 bg-emerald-500/10 text-emerald-400 rounded-xl border border-emerald-500/25 shadow-inner">
                <ShieldCheck className="w-6.5 h-6.5" />
              </span>
              <div>
                <h1 className="text-xl md:text-2xl font-bold tracking-tight text-white leading-tight font-sans">
                  PHP User Management <span className="text-emerald-400">Security Subsystem</span>
                </h1>
                <p className="text-slate-400 text-xs md:text-sm font-medium mt-0.5">
                  OWASP Top 10 Reference Architecture & Interactive Sandbox
                </p>
              </div>
            </div>
          </div>

          {/* System status dashboard capsule */}
          <div className="flex flex-wrap items-center gap-2.5">
            <div className="bg-slate-900/80 backdrop-blur border border-slate-800 px-3.5 py-2 rounded-xl flex items-center gap-2 shadow-xs">
              <span className="w-1.5 h-1.5 bg-emerald-500 rounded-full animate-pulse" />
              <span className="text-[10px] uppercase font-bold tracking-wider text-slate-400 font-mono">PDO Status:</span>
              <span className="text-[11px] font-semibold text-slate-200 font-mono">Secure Prepared</span>
            </div>
            <div className="bg-slate-900/80 backdrop-blur border border-slate-800 px-3.5 py-2 rounded-xl flex items-center gap-2 shadow-xs">
              <span className="w-1.5 h-1.5 bg-blue-400 rounded-full animate-pulse" />
              <span className="text-[10px] uppercase font-bold tracking-wider text-slate-400 font-mono">Crypt:</span>
              <span className="text-[11px] font-semibold text-slate-200 font-mono">Bcrypt Cost 10</span>
            </div>
            <div className="bg-slate-900/80 backdrop-blur border border-slate-800 px-3.5 py-2 rounded-xl flex items-center gap-2 shadow-xs">
              <span className="w-1.5 h-1.5 bg-amber-400 rounded-full animate-pulse" />
              <span className="text-[10px] uppercase font-bold tracking-wider text-slate-400 font-mono">Anti-CSRF:</span>
              <span className="text-[11px] font-semibold text-slate-200 font-mono">hash_equals</span>
            </div>
          </div>
        </div>
      </header>

      {/* Main Tabbed Toolbar Navigation */}
      <div className="bg-white/95 backdrop-blur-md border-b border-slate-200 sticky top-0 z-40 shadow-[0_2px_15px_-3px_rgba(0,0,0,0.03)] transition-all">
        <div className="max-w-7xl mx-auto px-6 md:px-8 flex space-x-1 overflow-x-auto scrollbar-none">
          {[
            { id: "playground", label: "Interactive Sandbox", icon: Play },
            { id: "rbac", label: "RBAC & Session Simulator", icon: Layers },
            { id: "codebase", label: "Annotated Code Explorer", icon: FileCode },
            { id: "docs", label: "Security Specifications", icon: BookOpen }
          ].map((tab) => {
            const IconComponent = tab.icon;
            const isSelected = activeTab === tab.id;
            return (
              <button
                key={tab.id}
                onClick={() => {
                  setActiveTab(tab.id as any);
                  if (tab.id === "rbac") {
                    setDashboardAccessResult({ status: "idle", log: "" });
                  }
                }}
                className={`relative flex items-center gap-2 py-4.5 px-4 font-semibold text-xs md:text-sm transition-all duration-200 cursor-pointer whitespace-nowrap outline-none ${
                  isSelected
                    ? "text-emerald-600"
                    : "text-slate-500 hover:text-slate-800"
                }`}
              >
                <IconComponent className={`w-4 h-4 transition-colors ${isSelected ? "text-emerald-500" : "text-slate-400"}`} />
                {tab.label}
                {isSelected && (
                  <motion.div
                    layoutId="activeTabUnderline"
                    className="absolute bottom-0 left-0 right-0 h-[2.5px] bg-emerald-600 rounded-t"
                    transition={{ type: "spring", stiffness: 350, damping: 30 }}
                  />
                )}
              </button>
            );
          })}
        </div>
      </div>

      {/* Main Content Area */}
      <main className="flex-grow max-w-7xl w-full mx-auto p-6 md:p-8">
        <AnimatePresence mode="wait">
          {activeTab === "playground" && (
            <motion.div
              key="playground"
              initial={{ opacity: 0, y: 10 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0, y: -10 }}
              transition={{ duration: 0.2 }}
              className="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start"
            >
              {/* Presets & Registration Input Panel */}
              <div className="lg:col-span-5 flex flex-col gap-6">
                {/* Attack / Legit Presets Block */}
                <div className="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-[0_1px_3px_0_rgba(0,0,0,0.05),0_1px_2px_0_rgba(0,0,0,0.03)]">
                  <div className="flex items-center gap-2.5 mb-2 border-b border-slate-100 pb-3">
                    <Fingerprint className="w-5 h-5 text-emerald-600" />
                    <div>
                      <h3 className="font-bold text-slate-900 text-sm tracking-tight">Interactive Security Presets</h3>
                      <p className="text-[11px] text-slate-400 font-mono mt-0.5">Payload Delivery Simulation</p>
                    </div>
                  </div>
                  <p className="text-xs text-slate-500 leading-relaxed mb-4">
                    Instantly load test parameters into the pipeline to watch how the input validation engine handles SQL Injections, sanitizes XSS tags, or vets password entropy.
                  </p>
                  
                  <div className="grid grid-cols-2 gap-2.5">
                    {[
                      {
                        id: "legit",
                        label: "Legit User",
                        desc: "Standard input parameters",
                        colorClass: "hover:border-emerald-300 hover:bg-emerald-50/20 hover:text-emerald-800",
                        badge: "✓ legit",
                        badgeClass: "bg-emerald-500/10 text-emerald-600"
                      },
                      {
                        id: "sqli",
                        label: "SQL Injection",
                        desc: "Escape quotes and OR clause",
                        colorClass: "hover:border-rose-300 hover:bg-rose-50/20 hover:text-rose-800",
                        badge: "⚡ attack",
                        badgeClass: "bg-rose-500/10 text-rose-600"
                      },
                      {
                        id: "xss",
                        label: "XSS Payload",
                        desc: "Active <script> tags",
                        colorClass: "hover:border-amber-300 hover:bg-amber-50/20 hover:text-amber-800",
                        badge: "☣ script",
                        badgeClass: "bg-amber-500/10 text-amber-600"
                      },
                      {
                        id: "weak",
                        label: "Weak Pass",
                        desc: "Low-entropy credentials",
                        colorClass: "hover:border-indigo-300 hover:bg-indigo-50/20 hover:text-indigo-800",
                        badge: "⚠️ weak",
                        badgeClass: "bg-indigo-500/10 text-indigo-600"
                      }
                    ].map((preset) => (
                      <button
                        key={preset.id}
                        type="button"
                        onClick={() => applyPreset(preset.id as any)}
                        className={`p-3 bg-slate-50 border border-slate-200/75 text-slate-700 text-xs rounded-xl hover:shadow-xs transition-all duration-200 cursor-pointer text-left flex flex-col justify-between h-[85px] ${preset.colorClass}`}
                      >
                        <div className="flex items-center justify-between w-full">
                          <span className="font-bold text-slate-900 tracking-tight">{preset.label}</span>
                          <span className={`text-[9px] font-mono font-bold px-1.5 py-0.2 rounded-md ${preset.badgeClass}`}>
                            {preset.badge}
                          </span>
                        </div>
                        <span className="text-[10px] text-slate-400 line-clamp-2 mt-1 leading-snug font-sans">
                          {preset.desc}
                        </span>
                      </button>
                    ))}
                  </div>
                </div>

                {/* Secure HTML Registration Form */}
                <div className="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-[0_2px_12px_-4px_rgba(0,0,0,0.05),0_10px_20px_-10px_rgba(0,0,0,0.04)] relative overflow-hidden">
                  <div className="absolute top-0 right-0 bg-slate-950 text-white text-[9px] font-mono uppercase tracking-wider px-3.5 py-1.5 rounded-bl-xl font-semibold border-l border-b border-slate-800 shadow">
                    Active Filters
                  </div>

                  <div className="flex items-center gap-2 mb-2 border-b border-slate-100 pb-3">
                    <User className="w-5 h-5 text-emerald-600" />
                    <div>
                      <h3 className="font-bold text-slate-900 text-sm tracking-tight">Simulated Registration Form</h3>
                      <p className="text-[11px] text-slate-400 font-mono mt-0.5">Dual-Layer Validation Pipeline</p>
                    </div>
                  </div>
                  <p className="text-xs text-slate-500 leading-relaxed mb-5">
                    Modeled after <code>/php/register.html</code>. Includes instant regex verification before form transmission.
                  </p>

                  <form onSubmit={handleSimulatedRegister} className="space-y-4">
                    {/* CSRF Selection Tool */}
                    <div className="bg-slate-50/70 p-3.5 rounded-xl border border-slate-200/70 mb-2">
                      <div className="flex justify-between items-center mb-2.5">
                        <label className="text-[10px] font-mono font-bold text-slate-500 uppercase tracking-wider flex items-center gap-1">
                          <Key className="w-3.5 h-3.5 text-slate-400" />
                          Anti-CSRF Configuration
                        </label>
                        <span className="text-[9px] bg-slate-200 text-slate-600 px-1.5 py-0.5 rounded-md font-mono font-bold">
                          Token Bind
                        </span>
                      </div>
                      <div className="grid grid-cols-3 gap-1.5">
                        {[
                          { id: "valid", label: "Valid Token", style: "bg-emerald-600 border-emerald-600 text-white shadow-xs" },
                          { id: "invalid", label: "Spoofed / Bad", style: "bg-rose-600 border-rose-600 text-white shadow-xs" },
                          { id: "missing", label: "No Token", style: "bg-amber-600 border-amber-600 text-white shadow-xs" }
                        ].map((opt) => {
                          const isSel = csrfOption === opt.id;
                          return (
                            <button
                              key={opt.id}
                              type="button"
                              onClick={() => setCsrfOption(opt.id as any)}
                              className={`px-2 py-1.5 text-[11px] rounded-lg border text-center transition-all duration-200 cursor-pointer font-semibold ${
                                isSel
                                  ? opt.style
                                  : "bg-white border-slate-200 text-slate-600 hover:bg-slate-100"
                              }`}
                            >
                              {opt.label}
                            </button>
                          );
                        })}
                      </div>
                    </div>

                    <div className="space-y-1.5">
                      <label className="block text-[11px] font-semibold text-slate-400 font-mono uppercase tracking-wider">
                        Username
                      </label>
                      <input
                        type="text"
                        value={username}
                        onChange={(e) => setUsername(e.target.value)}
                        className="w-full px-3.5 py-2.5 text-sm bg-slate-50/50 hover:bg-slate-50/80 focus:bg-white border border-slate-200 focus:border-emerald-500 focus:ring-2 focus:ring-emerald-500/20 rounded-xl focus:outline-none transition-all text-slate-800 font-medium"
                        placeholder="Alphanumeric, 3-20 characters"
                      />
                      {!/^[a-zA-Z0-9]{3,20}$/.test(username) && username.length > 0 && (
                        <p className="text-rose-500 text-[11px] mt-1.5 flex items-center gap-1 font-mono font-medium">
                          <AlertTriangle className="w-3.5 h-3.5" />
                          Regex validation failed (Letters/Numbers, 3-20 chars).
                        </p>
                      )}
                    </div>

                    <div className="space-y-1.5">
                      <label className="block text-[11px] font-semibold text-slate-400 font-mono uppercase tracking-wider">
                        Email Address
                      </label>
                      <input
                        type="email"
                        value={email}
                        onChange={(e) => setEmail(e.target.value)}
                        className="w-full px-3.5 py-2.5 text-sm bg-slate-50/50 hover:bg-slate-50/80 focus:bg-white border border-slate-200 focus:border-emerald-500 focus:ring-2 focus:ring-emerald-500/20 rounded-xl focus:outline-none transition-all text-slate-800 font-medium"
                        placeholder="user@securesystem.org"
                      />
                      {!/^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(email) && email.length > 0 && (
                        <p className="text-rose-500 text-[11px] mt-1.5 flex items-center gap-1 font-mono font-medium">
                          <AlertTriangle className="w-3.5 h-3.5" />
                          Email must match standard RFC format.
                        </p>
                      )}
                    </div>

                    <div className="space-y-1.5">
                      <label className="block text-[11px] font-semibold text-slate-400 font-mono uppercase tracking-wider">
                        Password
                      </label>
                      <div className="relative">
                        <input
                          type={showPassword ? "text" : "password"}
                          value={password}
                          onChange={(e) => setPassword(e.target.value)}
                          className="w-full pl-3.5 pr-10 py-2.5 text-sm bg-slate-50/50 hover:bg-slate-50/80 focus:bg-white border border-slate-200 focus:border-emerald-500 focus:ring-2 focus:ring-emerald-500/20 rounded-xl focus:outline-none transition-all text-slate-800 font-medium"
                          placeholder="Min 8 chars, 1 Upper, 1 Lower, 1 Num, 1 Spec"
                        />
                        <button
                          type="button"
                          onClick={() => setShowPassword(!showPassword)}
                          className="absolute right-3 top-3 text-slate-400 hover:text-slate-600 cursor-pointer"
                        >
                          {showPassword ? <EyeOff className="w-4 h-4" /> : <Eye className="w-4 h-4" />}
                        </button>
                      </div>

                      {/* Realtime password feedback */}
                      {password.length > 0 && (
                        <div className="mt-3 space-y-2 bg-slate-50/70 p-3.5 rounded-xl border border-slate-200/80 font-mono text-[11px] text-slate-600">
                          <span className="font-bold text-slate-400 text-[10px] tracking-wider block mb-1">PASSWORD ENTROPY METRICS</span>
                          <div className="grid grid-cols-1 sm:grid-cols-2 gap-1">
                            {[
                              { label: `Length (>= 8 chars: ${password.length})`, valid: password.length >= 8 },
                              { label: "At least 1 uppercase", valid: /[A-Z]/.test(password) },
                              { label: "At least 1 lowercase", valid: /[a-z]/.test(password) },
                              { label: "At least 1 digit", valid: /[0-9]/.test(password) },
                              { label: "At least 1 special char", valid: /[!@#$%^&*()_+={}\[\]|\\:;"'<>,.?\/-]/.test(password) }
                            ].map((rule, rIdx) => (
                              <div key={rIdx} className="flex items-center gap-1.5 py-0.5">
                                <span className={`flex items-center justify-center w-4 h-4 rounded-full font-bold text-[10px] ${rule.valid ? "bg-emerald-500/10 text-emerald-600" : "bg-slate-200 text-slate-400"}`}>
                                  {rule.valid ? "✓" : "✗"}
                                </span>
                                <span className={rule.valid ? "text-slate-700 font-medium" : "text-slate-400"}>
                                  {rule.label}
                                </span>
                              </div>
                            ))}
                          </div>
                        </div>
                      )}
                    </div>

                    <button
                      type="submit"
                      className="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-semibold py-3 rounded-xl text-xs uppercase tracking-wider flex items-center justify-center gap-2 shadow-xs hover:shadow-md active:scale-[0.98] transition-all cursor-pointer"
                    >
                      <ShieldCheck className="w-4 h-4" />
                      Register User through Subsystem
                    </button>
                  </form>
                </div>
              </div>

              {/* Security Logs & DB State Panels */}
              <div className="lg:col-span-7 flex flex-col gap-6">
                {/* Simulated Logs console */}
                <div className="bg-slate-950 text-slate-200 rounded-2xl border border-slate-900 shadow-xl flex flex-col h-[385px] overflow-hidden">
                  <div className="flex items-center justify-between px-4.5 py-3 bg-slate-900/80 backdrop-blur border-b border-slate-900">
                    <div className="flex items-center gap-2">
                      <Terminal className="w-4 h-4 text-emerald-400" />
                      <span className="text-[10px] font-mono font-bold tracking-wider text-slate-400 uppercase">
                        sub_system_sandbox_log.log
                      </span>
                    </div>
                    <button
                      onClick={() => setSandboxLog([])}
                      className="text-slate-400 hover:text-slate-200 text-[10px] font-mono border border-slate-800 hover:bg-slate-900 px-2.5 py-1 rounded-lg cursor-pointer transition-colors"
                    >
                      Clear Console
                    </button>
                  </div>
                  
                  <div className="p-5 flex-grow overflow-y-auto font-mono text-xs space-y-3.5 scrollbar-thin scrollbar-track-transparent scrollbar-thumb-slate-800">
                    {sandboxLog.length === 0 ? (
                      <p className="text-slate-600 italic text-center py-16">
                        No logs recorded. Interact with the form parameters or load a preset above to stream events.
                      </p>
                    ) : (
                      sandboxLog.map((log, index) => (
                        <div key={index} className="border-b border-slate-900/50 pb-3 last:border-0 last:pb-0">
                          <div className="flex items-center justify-between text-[10px] mb-1.5">
                            <span className="text-slate-500 font-mono">{log.timestamp}</span>
                            <span className={`px-2 py-0.5 rounded-md font-bold uppercase text-[9px] border font-mono ${
                              log.level === "success" 
                                ? "bg-emerald-500/10 text-emerald-400 border-emerald-500/20"
                                : log.level === "warning"
                                ? "bg-amber-500/10 text-amber-400 border-amber-500/20"
                                : log.level === "error"
                                ? "bg-rose-500/10 text-rose-400 border-rose-500/20"
                                : "bg-blue-500/10 text-blue-400 border-blue-500/20"
                            }`}>
                              {log.module}
                            </span>
                          </div>
                          <p className={`font-semibold tracking-tight ${
                            log.level === "success" 
                              ? "text-emerald-400" 
                              : log.level === "warning"
                              ? "text-amber-400"
                              : log.level === "error"
                              ? "text-rose-400"
                              : "text-slate-100"
                          }`}>
                            {log.message}
                          </p>
                          {log.details && (
                            <div className="bg-slate-900/50 p-2.5 rounded-lg border border-slate-900/80 mt-1.5">
                              <p className="text-[11px] text-slate-400 whitespace-pre-wrap leading-relaxed">
                                {log.details}
                              </p>
                            </div>
                          )}
                        </div>
                      ))
                    )}
                  </div>
                </div>

                {/* Simulated Database User Table */}
                <div className="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-[0_1px_3px_0_rgba(0,0,0,0.05),0_1px_2px_0_rgba(0,0,0,0.03)]">
                  <div className="flex items-center justify-between mb-4 pb-2 border-b border-slate-100">
                    <div className="flex items-center gap-2">
                      <Database className="w-5 h-5 text-emerald-600" />
                      <div>
                        <h3 className="font-bold text-slate-900 text-sm tracking-tight">Relational Database Table</h3>
                        <p className="text-[11px] text-slate-400 font-mono mt-0.5">MySQL users schema representation</p>
                      </div>
                    </div>
                    <span className="text-[11px] text-slate-500 font-mono bg-slate-100 px-2.5 py-1 rounded-lg font-semibold">
                      Connection: Active (PDO)
                    </span>
                  </div>

                  <div className="overflow-x-auto rounded-xl border border-slate-200/80">
                    <table className="w-full text-left font-mono text-xs border-collapse">
                      <thead>
                        <tr className="border-b border-slate-200 bg-slate-50/75 text-slate-500 text-[10px] uppercase font-bold tracking-wider">
                          <th className="p-3">id (INT)</th>
                          <th className="p-3">username (VARCHAR)</th>
                          <th className="p-3">email (VARCHAR)</th>
                          <th className="p-3">password_hash (VARCHAR - Bcrypt)</th>
                          <th className="p-3">role (ENUM)</th>
                        </tr>
                      </thead>
                      <tbody className="divide-y divide-slate-100">
                        {simulatedUsers.map((u) => (
                          <tr key={u.id} className="hover:bg-slate-50/50 transition-colors">
                            <td className="p-3 font-bold text-slate-400">{u.id}</td>
                            <td className="p-3 text-slate-900 font-bold">{u.username}</td>
                            <td className="p-3 text-slate-600 font-medium">{u.email}</td>
                            <td className="p-3 text-slate-400 select-all truncate max-w-[140px] text-[10px]" title={u.passwordHash}>
                              {u.passwordHash}
                            </td>
                            <td className="p-3">
                              <span className={`px-2 py-0.5 rounded-md text-[10px] font-bold font-mono tracking-wider uppercase border ${
                                u.role === "admin" 
                                  ? "bg-rose-50 border-rose-200 text-rose-800"
                                  : "bg-slate-100 border-slate-200 text-slate-800"
                              }`}>
                                {u.role}
                              </span>
                            </td>
                          </tr>
                        ))}
                      </tbody>
                    </table>
                  </div>
                </div>
              </div>
            </motion.div>
          )}

          {activeTab === "rbac" && (
            <motion.div
              key="rbac"
              initial={{ opacity: 0, y: 10 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0, y: -10 }}
              transition={{ duration: 0.2 }}
              className="max-w-5xl mx-auto space-y-8"
            >
              {/* Config Session options */}
              <div className="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-[0_1px_3px_0_rgba(0,0,0,0.05),0_1px_2px_0_rgba(0,0,0,0.03)]">
                <div className="flex items-center gap-2.5 mb-2.5 border-b border-slate-100 pb-3">
                  <Layers className="w-5 h-5 text-emerald-600" />
                  <div>
                    <h3 className="font-bold text-slate-900 text-lg tracking-tight">
                      Role-Based Access Control (RBAC) & Session Hijacking Safeguards
                    </h3>
                    <p className="text-xs text-slate-400 font-mono mt-0.5">SessionManager Gatekeeper Middleware</p>
                  </div>
                </div>
                <p className="text-sm text-slate-600 leading-relaxed mb-6">
                  Before any restricted dashboard script (such as <code>admin_dashboard.php</code>) processes its core logic, 
                  the <code>SessionManager::requireRole([&apos;admin&apos;])</code> validator executes. 
                  It decrypts the server session cookie, matches client-provided variables, checks User-Agent/IP fingerprint integrity, and validates clearances.
                </p>

                <div className="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
                  {/* Select Role Panel */}
                  <div className="lg:col-span-5 bg-slate-50/50 p-5 rounded-2xl border border-slate-200/80 space-y-5">
                    <label className="block text-[10px] font-bold text-slate-400 tracking-wider font-mono uppercase">
                      Select Simulated Session profile:
                    </label>
                    <div className="space-y-2.5">
                      {[
                        { id: "guest", label: "Unauthenticated Guest", desc: "No active session cookies present" },
                        { id: "user", label: "Standard User (Role: user)", desc: "Privileged only for basic consumer areas" },
                        { id: "editor", label: "Editor (Role: editor)", desc: "Privileged for content modification tools" },
                        { id: "admin", label: "Administrator (Role: admin)", desc: "Authorized full clearance credentials" }
                      ].map((role) => {
                        const isSel = selectedRbacRole === role.id;
                        return (
                          <button
                            key={role.id}
                            onClick={() => setSelectedRbacRole(role.id as any)}
                            className={`w-full text-left p-3.5 rounded-xl border transition-all duration-200 cursor-pointer block ${
                              isSel
                                ? "bg-emerald-600 border-emerald-600 text-white shadow-sm"
                                : "bg-white border-slate-200/80 text-slate-700 hover:bg-slate-100 hover:border-slate-300"
                            }`}
                          >
                            <span className="font-bold block text-sm tracking-tight mb-0.5">{role.label}</span>
                            <span className={`text-[11px] leading-snug block ${isSel ? "text-emerald-100" : "text-slate-400"}`}>
                              {role.desc}
                            </span>
                          </button>
                        );
                      })}
                    </div>

                    <button
                      onClick={handleRbacDashboardRequest}
                      className="w-full mt-2 bg-slate-950 hover:bg-slate-900 text-white text-xs uppercase font-semibold tracking-wider py-3 px-4 rounded-xl flex items-center justify-center gap-2 shadow-xs cursor-pointer transition-all active:scale-[0.98]"
                    >
                      <Play className="w-3.5 h-3.5" />
                      Request /admin_dashboard.php
                    </button>
                  </div>

                  {/* Simulator Panel Output */}
                  <div className="lg:col-span-7 flex flex-col justify-center h-full">
                    {/* Simulated Browser Webpage frame */}
                    <div className="bg-slate-950 rounded-2xl border border-slate-900 overflow-hidden shadow-lg flex flex-col min-h-[350px]">
                      {/* Browser address header mock */}
                      <div className="bg-slate-900 px-4 py-3 flex items-center gap-2 border-b border-slate-950">
                        {/* Browser dots */}
                        <div className="flex gap-1.5 mr-2">
                          <span className="w-3 h-3 bg-rose-500/80 rounded-full" />
                          <span className="w-3 h-3 bg-amber-500/80 rounded-full" />
                          <span className="w-3 h-3 bg-emerald-500/80 rounded-full" />
                        </div>
                        {/* Address container */}
                        <div className="flex-grow bg-slate-950/80 rounded-lg px-3 py-1 text-[11px] font-mono text-slate-400 flex items-center justify-between border border-slate-900/60">
                          <span className="flex items-center gap-1.5 truncate">
                            <span className="text-emerald-500 font-bold text-[10px]">https://</span>
                            <span className="text-slate-300 truncate">securesubsystem.local/admin_dashboard.php</span>
                          </span>
                          <span className="text-emerald-500 text-[10px] font-bold">🔒 Secure TLS</span>
                        </div>
                      </div>

                      {/* Browser main render canvas */}
                      <div className="bg-[#fcfdfd] flex-grow p-6 flex flex-col justify-center">
                        {dashboardAccessResult.status === "idle" && (
                          <div className="text-center p-8 text-slate-400">
                            <Lock className="w-12 h-12 mx-auto text-slate-300 mb-3.5" />
                            <h4 className="font-bold text-slate-700 text-sm tracking-tight">Authorization Gate Idle</h4>
                            <p className="text-xs text-slate-400 max-w-sm mx-auto mt-1 leading-relaxed">Select a session profile on the left, then dispatch the request to simulate middleware intercept behavior.</p>
                          </div>
                        )}

                        {dashboardAccessResult.status === "forbidden" && (
                          <div className="space-y-4 animate-fade-in">
                            <div className="bg-rose-50/70 border border-rose-200 p-5 rounded-2xl flex gap-3.5">
                              <div className="p-3 bg-rose-100 text-rose-600 rounded-xl self-start">
                                <BadgeAlert className="w-6 h-6" />
                              </div>
                              <div className="space-y-1">
                                <h4 className="text-rose-900 font-bold text-base tracking-tight">🚨 HTTP 403 Forbidden Access Restricted</h4>
                                <p className="text-rose-700 text-xs leading-relaxed">
                                  <strong>SessionManager::requireRole()</strong> has executed a fail-closed routine. Current credentials lack the necessary security attributes.
                                </p>
                                <div className="grid grid-cols-2 gap-2 text-[10px] text-rose-600 font-mono font-bold pt-2.5 uppercase tracking-wider">
                                  <span>✓ Session Aborted</span>
                                  <span>✓ Telemetry Recorded</span>
                                  <span>✓ Headers Injected</span>
                                  <span>✓ Zero DB Read Leak</span>
                                </div>
                              </div>
                            </div>

                            {/* Auditing log console block */}
                            <div className="bg-slate-950 text-rose-400 p-4 rounded-xl font-mono text-[11px] leading-relaxed max-h-[150px] overflow-y-auto border border-slate-900">
                              <span className="text-slate-500 font-bold block border-b border-slate-900 pb-1.5 mb-2 uppercase text-[9px] tracking-wider font-mono">
                                Security Audit Incident Log:
                              </span>
                              {dashboardAccessResult.log}
                            </div>
                          </div>
                        )}

                        {dashboardAccessResult.status === "granted" && (
                          <div className="space-y-4 animate-fade-in">
                            <div className="bg-emerald-50/70 border border-emerald-200 p-5 rounded-2xl flex gap-3.5">
                              <div className="p-3 bg-emerald-100 text-emerald-600 rounded-xl self-start animate-pulse">
                                <ShieldCheck className="w-6 h-6" />
                              </div>
                              <div className="space-y-1">
                                <h4 className="text-emerald-900 font-bold text-base tracking-tight">🔓 HTTP 200 OK Clearance Granted</h4>
                                <p className="text-emerald-700 text-xs leading-relaxed">
                                  Session validated successfully. Middleware passed execution to the <strong>Admin Dashboard</strong> module.
                                </p>
                              </div>
                            </div>

                            {/* Dashboard mockup representation */}
                            <div className="bg-white border border-slate-200 rounded-2xl p-4.5 text-slate-800 space-y-3.5 shadow-sm font-mono text-xs">
                              <div className="flex justify-between items-center border-b border-slate-100 pb-2.5">
                                <span className="font-bold text-slate-900 flex items-center gap-1.5">
                                  <LockOpen className="w-3.5 h-3.5 text-emerald-600" />
                                  Administrative Control Dashboard
                                </span>
                                <span className="bg-rose-100 border border-rose-200 text-rose-800 px-2 py-0.5 rounded-md text-[9px] font-bold tracking-wider font-mono uppercase">
                                  SYSTEM ADMIN
                                </span>
                              </div>
                              <p className="text-[11px] text-slate-500 italic leading-relaxed">
                                Secure panel assets compiled cleanly. Transport channel secured. Frame nesting protected.
                              </p>
                              <div className="grid grid-cols-2 gap-2 text-[10px] text-slate-600 bg-slate-50 p-2.5 rounded-lg border border-slate-100">
                                <p><strong>Clearance ID:</strong> admin_user_01</p>
                                <p><strong>Transport:</strong> TLS 1.3 Secure</p>
                                <p><strong>Token Type:</strong> HTTPOnly Cookie</p>
                                <p><strong>CSRF Token:</strong> Valid Bind</p>
                              </div>
                            </div>

                            <div className="bg-slate-950 text-emerald-400 p-4 rounded-xl font-mono text-[11px] leading-relaxed max-h-[150px] overflow-y-auto border border-slate-900">
                              <span className="text-slate-500 font-bold block border-b border-slate-900 pb-1.5 mb-2 uppercase text-[9px] tracking-wider font-mono">
                                Security Audit System Logs:
                              </span>
                              {dashboardAccessResult.log}
                            </div>
                          </div>
                        )}
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              {/* Secure headers check sheet */}
              <div className="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-[0_1px_3px_0_rgba(0,0,0,0.05),0_1px_2px_0_rgba(0,0,0,0.03)] space-y-4">
                <div>
                  <h4 className="font-bold text-slate-900 text-sm uppercase font-mono tracking-wider">
                    Configured Defense Headers (MITRE ATT&amp;CK mitigations)
                  </h4>
                  <p className="text-xs text-slate-500 mt-0.5 leading-relaxed">HTTP security response headers injected automatically at startup to protect downstream clients.</p>
                </div>
                
                <div className="grid grid-cols-1 md:grid-cols-3 gap-4.5">
                  {[
                    {
                      title: "X-Frame-Options: DENY",
                      desc: "Disables nesting execution inside any third-party Frame/iFrame entirely. Neutralizes click-jacking and click-hijacking vectors on critical controls."
                    },
                    {
                      title: "X-Content-Type-Options: nosniff",
                      desc: "Instructs browsers to strictly respect defined MIME types. Prevents attackers from disguising executable scripts inside passive text payloads."
                    },
                    {
                      title: "Content-Type: text/html; charset=UTF-8",
                      desc: "Enforces a strict UTF-8 character lookup stack. Prevents attackers from bypassing input validations with alternate character representation exploits."
                    }
                  ].map((header, hIdx) => (
                    <div key={hIdx} className="bg-slate-50/70 p-4 rounded-xl border border-slate-200/60 hover:border-slate-300 transition-colors">
                      <span className="font-mono text-xs text-slate-900 font-bold block mb-1.5 tracking-tight border-b border-slate-200 pb-1">{header.title}</span>
                      <p className="text-xs text-slate-600 leading-relaxed font-sans">
                        {header.desc}
                      </p>
                    </div>
                  ))}
                </div>
              </div>
            </motion.div>
          )}

          {activeTab === "codebase" && (
            <motion.div
              key="codebase"
              initial={{ opacity: 0, y: 10 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0, y: -10 }}
              transition={{ duration: 0.2 }}
              className="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start"
            >
              {/* Left Selector Tab Rail */}
              <div className="lg:col-span-3 flex flex-col gap-2">
                <span className="px-3 text-[10px] font-mono font-bold text-slate-400 uppercase tracking-widest mb-1.5">
                  Subsystem File Layout
                </span>
                <div className="space-y-1.5">
                  {PHP_CODEBASE.map((file, idx) => {
                    const isSelected = selectedFileIndex === idx;
                    return (
                      <button
                        key={idx}
                        onClick={() => {
                          setSelectedFileIndex(idx);
                          setHoveredAnnotation(null);
                        }}
                        className={`w-full text-left p-3.5 rounded-xl border text-xs font-mono font-bold flex items-center justify-between gap-3.5 transition-all duration-200 cursor-pointer ${
                          isSelected
                            ? "bg-slate-950 border-slate-950 text-emerald-400 shadow-md"
                            : "bg-white border-slate-200 text-slate-700 hover:bg-slate-50 hover:border-slate-300"
                        }`}
                      >
                        <div className="flex items-center gap-2.5 truncate w-full">
                          <FileCode className={`w-4.5 h-4.5 flex-shrink-0 ${isSelected ? "text-emerald-400" : "text-slate-400"}`} />
                          <div className="truncate text-left w-full">
                            <span className={`block text-[10px] font-normal font-sans mb-0.5 ${isSelected ? "text-slate-400" : "text-slate-400"}`}>{file.name}</span>
                            <span className="block text-xs font-semibold font-mono truncate">{file.path.split("/").pop()}</span>
                          </div>
                        </div>
                      </button>
                    );
                  })}
                </div>
              </div>

              {/* Code Viewer Panel */}
              <div className="lg:col-span-9 flex flex-col gap-6">
                <div className="bg-slate-950 rounded-2xl border border-slate-900 shadow-xl overflow-hidden flex flex-col">
                  {/* File Path Header */}
                  <div className="flex items-center justify-between px-4.5 py-3.5 bg-slate-900 border-b border-slate-950 text-xs text-slate-400 font-mono">
                    <span className="flex items-center gap-2 text-slate-300 font-semibold tracking-tight">
                      <HardDrive className="w-4 h-4 text-emerald-400" />
                      {PHP_CODEBASE[selectedFileIndex].path}
                    </span>
                    <span className="bg-slate-950 text-emerald-400 border border-slate-800 px-2.5 py-1 rounded-md font-bold uppercase text-[9px] tracking-wider font-mono">
                      {PHP_CODEBASE[selectedFileIndex].language}
                    </span>
                  </div>

                  {/* Code Block with line numbering */}
                  <div className="p-5 overflow-x-auto font-mono text-xs text-slate-200 leading-relaxed bg-slate-950 flex select-text">
                    <div className="text-slate-600 select-none text-right pr-4.5 border-r border-slate-900 mr-4.5 space-y-1">
                      {PHP_CODEBASE[selectedFileIndex].code.split("\n").map((_, i) => (
                        <div key={i} className="h-5">{i + 1}</div>
                      ))}
                    </div>
                    <div className="flex-grow space-y-1">
                      {PHP_CODEBASE[selectedFileIndex].code.split("\n").map((line, i) => {
                        const annotationIndex = PHP_CODEBASE[selectedFileIndex].annotations.findIndex(
                          a => a.line === i + 1
                        );
                        const isAnnotated = annotationIndex !== -1;

                        return (
                          <div
                            key={i}
                            className={`h-5 flex items-center transition-all duration-150 ${
                              isAnnotated
                                ? "bg-emerald-500/10 font-bold border-l-2 border-emerald-500 pl-2 hover:bg-emerald-500/15"
                                : ""
                            }`}
                            onMouseEnter={() => isAnnotated && setHoveredAnnotation(annotationIndex)}
                            onMouseLeave={() => isAnnotated && setHoveredAnnotation(null)}
                          >
                            <span className="whitespace-pre">{line}</span>
                            {isAnnotated && (
                              <span className="ml-3 bg-emerald-500 text-slate-950 px-2 py-0.2 rounded-md text-[8px] font-sans font-bold uppercase tracking-wider cursor-help">
                                secure hook
                              </span>
                            )}
                          </div>
                        );
                      })}
                    </div>
                  </div>
                </div>

                {/* Annotation Detail Box */}
                <div className="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-[0_1px_3px_0_rgba(0,0,0,0.05),0_1px_2px_0_rgba(0,0,0,0.03)]">
                  <div className="flex items-center gap-2.5 mb-2 pb-2.5 border-b border-slate-100">
                    <ShieldCheck className="w-5 h-5 text-emerald-600" />
                    <div>
                      <h3 className="font-bold text-slate-900 text-sm tracking-tight">Security Code Explanations</h3>
                      <p className="text-[11px] text-slate-400 font-mono mt-0.5">Interactive Audit Inspections</p>
                    </div>
                  </div>
                  <p className="text-xs text-slate-500 leading-relaxed mb-5">
                    Hover over or click highlighted <span className="text-emerald-600 font-bold font-mono">secure hook</span> labels in the visual editor above to jump and focus specific code security designs.
                  </p>

                  <div className="space-y-3">
                    {PHP_CODEBASE[selectedFileIndex].annotations.map((ann, idx) => {
                      const isHovered = hoveredAnnotation === idx;
                      return (
                        <div
                          key={idx}
                          className={`p-4 rounded-xl border transition-all duration-300 ${
                            isHovered
                              ? "bg-emerald-50/50 border-emerald-500 ring-2 ring-emerald-500/20 translate-x-1"
                              : "bg-slate-50/60 border-slate-100 hover:bg-slate-50 hover:border-slate-200"
                          }`}
                        >
                          <div className="flex items-center justify-between mb-2">
                            <span className="font-bold text-xs md:text-sm text-slate-900 flex items-center gap-2">
                              <span className="bg-emerald-600 text-white text-[10px] font-mono font-bold px-2 py-0.5 rounded-lg tracking-wider">
                                LINE {ann.line}
                              </span>
                              {ann.title}
                            </span>
                            <span className="text-[9px] bg-slate-200 text-slate-700 font-bold font-mono px-2 py-0.5 rounded-md tracking-wider uppercase">
                              mitigation
                            </span>
                          </div>
                          <p className="text-xs text-slate-600 leading-relaxed font-sans">
                            {ann.description}
                          </p>
                        </div>
                      );
                    })}
                  </div>
                </div>
              </div>
            </motion.div>
          )}

          {activeTab === "docs" && (
            <motion.div
              key="docs"
              initial={{ opacity: 0, y: 10 }}
              animate={{ opacity: 1, y: 0 }}
              exit={{ opacity: 0, y: -10 }}
              transition={{ duration: 0.2 }}
              className="max-w-4xl mx-auto"
            >
              <div className="bg-white p-7 md:p-9 rounded-2xl border border-slate-200/80 shadow-[0_2px_12px_-4px_rgba(0,0,0,0.05),0_10px_20px_-10px_rgba(0,0,0,0.04)] space-y-6">
                <div className="flex items-center gap-3 border-b border-slate-100 pb-5">
                  <span className="p-2.5 bg-emerald-500/10 text-emerald-600 rounded-xl border border-emerald-500/20">
                    <FileText className="w-6.5 h-6.5" />
                  </span>
                  <div>
                    <h2 className="text-lg md:text-xl font-bold text-slate-950 font-sans tracking-tight">
                      Technical Architecture Security Report
                    </h2>
                    <p className="text-xs text-slate-400 font-mono mt-0.5">Dual-Layer Defense Specifications Summary</p>
                  </div>
                </div>

                <div className="prose prose-slate max-w-none text-slate-600 space-y-6 text-xs md:text-sm leading-relaxed font-sans">
                  {SECURITY_REPORT_MD.split("\n\n").map((section, idx) => {
                    if (section.startsWith("###")) {
                      return (
                        <h3 key={idx} className="text-sm md:text-base font-bold text-slate-900 border-b border-slate-100 pb-1.5 mt-8 font-sans tracking-tight uppercase tracking-wide">
                          {section.replace("###", "").trim()}
                        </h3>
                      );
                    }
                    if (section.startsWith("* **")) {
                      return (
                        <div key={idx} className="pl-4.5 border-l-3 border-emerald-500/80 space-y-3 py-1.5 bg-slate-50/60 rounded-r-xl p-4 border border-l-0 border-slate-200/30">
                          {section.split("\n").map((bullet, bIdx) => {
                            const cleanText = bullet.replace("*", "").trim();
                            const parts = cleanText.split("**");
                            if (parts.length >= 3) {
                              return (
                                <p key={bIdx} className="text-xs leading-relaxed text-slate-700">
                                  <strong className="text-slate-900 font-semibold">{parts[1]}</strong>
                                  {parts.slice(2).join("")}
                                </p>
                              );
                            }
                            return <p key={bIdx} className="text-xs leading-relaxed text-slate-700">{cleanText}</p>;
                          })}
                        </div>
                      );
                    }
                    if (section.startsWith("####")) {
                      return (
                        <h4 key={idx} className="font-mono text-xs font-bold text-slate-800 uppercase tracking-widest pt-4 border-b border-slate-100/50 pb-1">
                          {section.replace("####", "").trim()}
                        </h4>
                      );
                    }
                    return <p key={idx} className="text-slate-600 leading-relaxed font-sans text-xs md:text-sm">{section}</p>;
                  })}
                </div>
              </div>
            </motion.div>
          )}
        </AnimatePresence>
      </main>

      {/* Persistent Footer */}
      <footer className="bg-slate-950 text-slate-500 py-6 px-6 border-t border-slate-900 text-center font-mono text-[11px] relative overflow-hidden">
        <div className="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
          <p className="text-slate-400">© 2026 PHP Security Subsystem Playground • Secure Reference Architecture</p>
          <p className="text-slate-500">Designed strictly under OWASP Top 10 guidelines</p>
        </div>
      </footer>
    </div>
  );
}
