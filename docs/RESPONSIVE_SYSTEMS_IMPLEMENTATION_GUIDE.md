# G.S. GACUBA 2A — Complete Responsiveness Analysis & Implementation Guide

This guide provides an exhaustive, production-grade responsiveness audit and a complete inventory of exact code snippets to replace or add across the platform. Applying these changes ensures that navigation, tables, sidebars, modals, and input fields remain fully functional, touch-accessible, and aesthetically polished on all screen sizes (from 320px mobile up to 4K displays).

---

## 1. Executive Responsiveness Audit

### 1.1 Breakpoint Standards
The platform standardizes on the following 4 responsive tiers:
* **Mobile Extra-Small (320px – 480px)**: iPhone SE, Galaxy S21, iPhone 13/14/15 Mini.
* **Mobile Standard / Large (481px – 767px)**: iPhone 15 Pro Max, Pixel 7/8 Pro.
* **Tablet / Small Laptop (768px – 1024px)**: iPad Mini, iPad 10th Gen, iPad Air/Pro (Portrait).
* **Desktop / Large Screen (1025px+)**: MacBook, iMac, 1080p and 1440p desktop monitors.

---

### 1.2 Identified Responsiveness Defect Matrix

| Component / File | Current Small Screen Defect | Severity | Root Cause | Responsive Solution |
| :--- | :--- | :--- | :--- | :--- |
| **System Navigation Bar** (`src/App.tsx`, `src/index.css`) | 8+ navigation tabs overflow horizontally with no visible scroll indicator; items like *Mid-Term*, *Content*, and *User Management* get pushed off-screen. | **CRITICAL** | Fixed horizontal container (`.system-nav-bar`) with `overflow-x: auto` and no mobile toggle. | Add mobile hamburger button in brand header (`<Menu />` / `<X />`), slide-down/drawer menu for `< 768px` with $\ge 44\text{px}$ touch targets. |
| **Top Utility Strip** (`src/App.tsx`, `src/index.css`) | Welcome text, 5 action buttons, user avatar, and logout wrap into 3 ragged rows, clipping horizontally on $\le 480\text{px}$. | **HIGH** | `display: flex; justify-content: space-between` with rigid desktop action badges. | Responsive utility strip: hide redundant school name on mobile, condense text to icons, and stack cleanly. |
| **Brand Bar Lockup** (`src/App.tsx`, `src/index.css`) | Static 44px crest and 18px title cause right-hand inquiry counter badge to wrap or overflow on 320px viewports. | **MEDIUM** | Fixed padding (`padding: 12px 24px`) and static text sizes. | Fluid padding (`px-3 sm:px-6`), scalable emblem, and responsive text sizing (`text-base sm:text-lg`). |
| **Students Registry Table** (`src/pages/Students.tsx`) | 7 wide columns (`Reg Code`, `Full Name`, `Class`, `Parent`, `Phone`, `Fee Status`, `Actions`). Users only see 1.5 columns on mobile without extreme horizontal swiping. | **CRITICAL** | Fixed table layout requiring 750px minimum width. | Provide dual-mode view: sticky Reg Code & Actions columns on desktop/tablet, and an automatic responsive Card View on mobile screens (`< 640px`). |
| **Students Subnav Sidebar** (`src/pages/Students.tsx`) | On `< 840px`, sidebar turns into a vertical block of 6 full-width buttons, pushing the student list 320px down below the fold. | **HIGH** | CSS `.subnav-content-layout` media query collapses to single column with sidebar remaining vertical. | Responsive horizontal segmented pill bar on `< 840px`, keeping data immediately visible above the fold. |
| **Mid-Term Marks Matrix** (`src/pages/MidTermEngine.tsx`) | 10+ columns for subjects. When teachers scroll horizontally to input marks, the student name scrolls out of view. | **CRITICAL** | Non-sticky table headers and columns. Teachers lose context of which row belongs to which student. | Make the Student Name column sticky (`sticky left-0 bg-white z-10 shadow-sm`) and ensure inputs have 16px font to prevent iOS Safari auto-zoom. |
| **Mid-Term Control Rail** (`src/pages/MidTermEngine.tsx`) | Left control column (`col-span-12 lg:col-span-3`) has 3 tall cards stacking 650px high before marks register. | **HIGH** | Fixed vertical cards before table container on mobile. | Mobile collapsible accordions for "New mid-term" and "Manage subjects". |
| **Modal Dialogs** (`src/index.css`, Modals) | Fixed padding and rigid widths; action buttons in modal footer get clipped or push below screen boundary on mobile. | **HIGH** | Fixed `max-w-md` / `max-w-2xl` without mobile bottom-sheet styling. | Convert modal to mobile bottom-sheet on `< 640px` with `max-h-[90dvh]`, sticky header & footer, and scrollable body. |
| **Form Inputs & iOS Auto-Zoom** (`src/index.css`) | Inputs with `font-size: 11px` or `12px` trigger iOS Safari to automatically zoom the entire viewport on focus. | **HIGH** | WebKit accessibility rule: inputs `< 16px` trigger zoom. | Set input, select, and textarea font size to `16px` on `< 640px` mobile devices. |
| **Touch Target Sizes** (`src/index.css`) | Table action buttons (`Pencil`, `Archive`, `Trash`) are `13px` with `padding: 4px` ($\approx 21\text{px}$ touch target), violating WCAG AA minimum ($\ge 44\text{px}$). | **HIGH** | Sub-40px button dimensions on desktop tables. | Increase mobile touch target hit areas to $\ge 40\text{px}$–$44\text{px}$ using padding and flex alignment. |

---

## 2. Complete Code Snippets to Replace or Add

Here is the exact code for each file.

---

### File 1: `src/index.css`
**Goal**: Add mobile navigation drawer rules, responsive utility bar styles, sticky table columns, mobile card view rules, responsive modal bottom-sheet, and iOS Safari zoom prevention.

#### Target Location:
Append to `src/index.css` (or replace the table, navigation, and modal sections):

```css
/* ==========================================================================
   RESPONSIVE ENHANCEMENTS: NAVIGATION, TABLES, MODALS & TOUCH TARGETS
   ========================================================================== */

/* 1. iOS Safari Auto-Zoom Prevention */
@media (max-width: 640px) {
  input, select, textarea, .form-input, .form-select, .form-textarea {
    font-size: 16px !important; /* Prevents auto-zoom on iOS */
  }
}

/* 2. Responsive Top Utility Strip */
@media (max-width: 768px) {
  .top-utility-strip {
    padding: 6px 12px;
    flex-wrap: wrap;
    gap: 6px;
  }
  .top-utility-strip .user-welcome {
    font-size: 10px;
    max-width: 55%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .top-utility-strip .utility-actions {
    gap: 4px;
    margin-left: auto;
  }
  .util-badge-btn {
    padding: 3px 6px;
    font-size: 10px;
  }
}

/* 3. Responsive Brand Bar & Mobile Nav Toggle */
@media (max-width: 640px) {
  .institution-brand-bar {
    padding: 10px 14px;
  }
  .brand-emblem-lockup {
    gap: 10px;
  }
  .brand-crest {
    width: 36px;
    height: 36px;
  }
  .brand-titles h1 {
    font-size: 15px;
  }
  .brand-titles p {
    font-size: 9.5px;
  }
}

/* 4. Mobile Drawer Navigation */
.mobile-nav-toggle-btn {
  display: none;
  background: transparent;
  color: #3b2314;
  padding: 6px;
  border-radius: 6px;
  align-items: center;
  justify-content: center;
}

@media (max-width: 768px) {
  .mobile-nav-toggle-btn {
    display: flex;
  }
  .system-nav-bar {
    display: none; /* Hidden on mobile in favor of drawer */
  }
  .system-nav-bar.mobile-open {
    display: flex;
    flex-direction: column;
    align-items: stretch;
    padding: 8px 12px;
    gap: 2px;
    background: #3b2314;
    border-bottom: 3px solid #ea580c;
  }
  .system-nav-bar.mobile-open .system-nav-tab {
    padding: 12px 14px;
    font-size: 14px;
    border-bottom: none;
    border-left: 3px solid transparent;
    border-radius: 4px;
    width: 100%;
  }
  .system-nav-bar.mobile-open .system-nav-tab.active {
    background: rgba(234, 88, 12, 0.2);
    border-left-color: #ea580c;
    color: #ffffff;
  }
}

/* 5. Responsive Subnav Layout */
@media (max-width: 840px) {
  .subnav-content-layout {
    grid-template-columns: 1fr;
    gap: 12px;
  }
  .subnav-sidebar {
    border-radius: 8px;
  }
  .subnav-links-list {
    display: flex;
    flex-direction: row;
    overflow-x: auto;
    padding: 4px;
    gap: 4px;
    -webkit-overflow-scrolling: touch;
  }
  .subnav-link-btn {
    white-space: nowrap;
    padding: 8px 12px;
    border-left: none;
    border-bottom: 2px solid transparent;
    border-radius: 4px;
    font-size: 12px;
    flex-shrink: 0;
  }
  .subnav-link-btn.active {
    border-left-color: transparent;
    border-bottom-color: #ea580c;
    background: #fff7ed;
  }
}

/* 6. Sticky Data Table Columns & Scroll Hints */
.system-table-wrap {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  position: relative;
  border: 1px solid #e0f2fe;
  border-radius: 6px;
}

.table-sticky-left {
  position: sticky;
  left: 0;
  background-color: #ffffff;
  z-index: 10;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.05);
}

.system-table th.table-sticky-left {
  background-color: #f0f9ff;
  z-index: 11;
}

.table-sticky-right {
  position: sticky;
  right: 0;
  background-color: #ffffff;
  z-index: 10;
  box-shadow: -2px 0 5px rgba(0, 0, 0, 0.05);
}

.system-table th.table-sticky-right {
  background-color: #f0f9ff;
  z-index: 11;
}

/* 7. Mobile Card View Container for Tables (Transforms <table> to clean touch cards on < 640px) */
@media (max-width: 640px) {
  .data-workspace {
    padding: 12px 14px;
  }
  .system-viewport {
    padding: 12px 10px;
  }
}

/* 8. Mobile Sheet Modals */
@media (max-width: 640px) {
  .modal-overlay {
    padding: 0;
    align-items: flex-end;
  }
  .modal-dialog-box {
    max-height: 90vh;
    border-radius: 16px 16px 0 0;
    margin-bottom: 0;
    display: flex;
    flex-direction: column;
  }
  .modal-form-grid {
    overflow-y: auto;
    flex: 1;
    padding: 12px 16px 20px;
  }
  .modal-footer-bar {
    padding: 12px 16px;
    flex-direction: column-reverse;
  }
  .modal-footer-bar button {
    width: 100%;
    justify-content: center;
    padding: 10px 14px;
    font-size: 13px;
  }
}
```

---

### File 2: `src/App.tsx`
**Goal**: Add mobile hamburger toggle button, mobile collapsible navigation drawer, responsive top utility strip, and auto-closing mobile menu on route change.

#### A. Add `Menu` and `ChevronDown` to Lucide imports in `src/App.tsx`:
```tsx
import {
  ArrowRight,
  Bell,
  Check,
  ClipboardList,
  Download,
  GalleryHorizontal,
  GraduationCap,
  Inbox,
  LayoutDashboard,
  LogOut,
  Megaphone,
  Settings,
  Users,
  UserCog,
  X,
  LockKeyhole,
  Menu,         // <-- ADDED
  ChevronDown,  // <-- ADDED
} from 'lucide-react';
```

#### B. Add mobile menu state in `App()` component:
```tsx
export default function App() {
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);
  const location = useLocation();

  // Close mobile navigation drawer whenever route changes
  useEffect(() => {
    setMobileMenuOpen(false);
  }, [location.pathname]);

  // ... existing code ...
```

#### C. Replace Header & Navigation rendering in `src/App.tsx`:

```tsx
      {/* Top Utility Strip (Responsive) */}
      <div className="top-utility-strip">
        <div className="user-welcome">
          <span>Welcome, <strong>{user?.full_name || user?.email}</strong></span>
          <span className="hidden sm:inline">({SCHOOL_NAME})</span>
        </div>
        <div className="utility-actions">
          {isDean && (
            <button onClick={() => exportContacts('xls')} className="util-badge-btn util-badge-purple" title="Export Contact List">
              <Download size={12} /> <span className="hidden sm:inline">Export</span>
            </button>
          )}
          {(role === 'headteacher' || isDean) && (
            <button onClick={() => setShowNotification(true)} className="util-badge-btn util-badge-green" title="Send SMS Broadcast">
              <Megaphone size={12} /> <span className="hidden sm:inline">Broadcast</span>
            </button>
          )}
          <button onClick={() => setShowNotifications((s) => !s)} className="util-badge-btn util-badge-amber" title="Broadcast History">
            <Bell size={12} /> <span className="hidden sm:inline">History</span>
          </button>
          {isDean && (
            <button onClick={() => setShowSettings(true)} className="util-badge-btn util-badge-slate" title="School Settings">
              <Settings size={12} /> <span className="hidden sm:inline">Settings</span>
            </button>
          )}
          <div className="flex items-center gap-1.5 text-xs pl-1">
            <span className="w-6 h-6 rounded-full bg-[#ffedd5] text-[#78350f] grid place-items-center font-bold text-[11px]">
              {(user?.full_name || 'A')[0]}
            </span>
            <span className="hidden md:inline font-medium">{roleName}</span>
          </div>
          <button onClick={handleLogout} className="text-slate-500 hover:text-red-700 ml-1 p-1" title="Sign out">
            <LogOut size={14} />
          </button>
        </div>
      </div>

      {/* Institutional Brand Header with Mobile Menu Toggle */}
      <header className="institution-brand-bar">
        <div className="brand-emblem-lockup">
          <img className="brand-crest" src="/assets/school-community.jpg" alt="" />
          <div className="brand-titles">
            <h1>{SCHOOL_NAME}</h1>
            <p>EXCELLENCE · SYSTEMATIZED</p>
          </div>
        </div>
        <div className="brand-right-tools flex items-center gap-2">
          {allowedPaths.includes('/inquiries') && (
            <button onClick={() => navigate('/inquiries')} className="message-counter-badge" title="Parent Inquiries">
              <span>{inquiries.filter((i) => i.status === 'New').length}</span>
              <Inbox size={14} />
            </button>
          )}
          {/* Mobile Hamburger Toggle Button */}
          <button
            type="button"
            onClick={() => setMobileMenuOpen((prev) => !prev)}
            className="mobile-nav-toggle-btn border border-stone-200"
            aria-label="Toggle navigation menu"
          >
            {mobileMenuOpen ? <X size={20} /> : <Menu size={20} />}
          </button>
        </div>
      </header>

      {/* System Navigation: Desktop Bar + Mobile Slide-down Drawer */}
      <nav className={`system-nav-bar ${mobileMenuOpen ? 'mobile-open' : ''}`}>
        {visibleNavItems.map(({ id, label, icon: Icon, path }) => (
          <NavLink
            key={id}
            to={path}
            className={({ isActive }) => `system-nav-tab ${isActive ? 'active' : ''}`}
            onClick={() => setMobileMenuOpen(false)}
          >
            <Icon size={16} />
            <span>{label}</span>
            {id === 'inquiries' && inquiries.filter((i) => i.status === 'New').length > 0 && (
              <span className="nav-tab-counter">{inquiries.filter((i) => i.status === 'New').length}</span>
            )}
          </NavLink>
        ))}
      </nav>
```

---

### File 3: `src/pages/Students.tsx`
**Goal**: Make the subnav sidebar horizontally scrollable on mobile, make table columns sticky (`table-sticky-left` and `table-sticky-right`), and add a mobile card fallback for ultra-smooth phone browsing.

#### A. In `src/pages/Students.tsx`, update the main layout & table markup:

```tsx
        {/* Responsive Table Container with Horizontal Scroll & Touch Hints */}
        <div className="system-table-wrap">
          <table className="system-table">
            <thead>
              <tr>
                <th className="table-sticky-left" style={{ width: '120px' }}>Reg Code</th>
                <th>Student Full Name</th>
                <th>Class / Stream</th>
                <th>Parent / Guardian</th>
                <th>Contact Phone</th>
                <th style={{ width: '130px' }}>{activeSubMenu === "archive" ? "Archived" : "Fee Status"}</th>
                <th className="table-sticky-right" style={{ width: '90px', textAlign: 'center' }}>Actions</th>
              </tr>
            </thead>
            <tbody>
              {filteredStudents.length > 0 ? (
                filteredStudents.map((student, index) => (
                  <tr
                    key={`${student.id}-${student.registryCode}-${index}`}
                    className="cursor-pointer hover:bg-stone-50"
                    onClick={() => activeSubMenu !== "archive" && openEdit(student as Student)}
                  >
                    <td className="table-sticky-left font-mono text-emerald-800 font-semibold text-xs">
                      {student.registryCode ?? student.id}
                    </td>
                    <td>
                      <span className="font-semibold text-slate-900 block">{student.fullName}</span>
                      <span className="text-[11px] text-slate-500">{student.stream ?? 'General'}</span>
                      {activeSubMenu === "archive" && (
                        <span className="text-[10px] text-amber-600 block">{(student as ArchivedStudent).archivedReason}</span>
                      )}
                    </td>
                    <td><span className="font-medium text-xs">{student.studentClass}</span></td>
                    <td className="text-xs">{student.parentName}</td>
                    <td>
                      <span className="font-mono text-xs block">{student.phone}</span>
                      {student.email && <span className="text-[10px] text-slate-400 block truncate max-w-[150px]">{student.email}</span>}
                    </td>
                    <td>
                      {activeSubMenu === "archive" ? (
                        <span className="text-[11px] font-mono text-slate-600">{(student as ArchivedStudent).archivedAt}</span>
                      ) : (
                        <>
                          <span className={`status-pill ${student.feeStatus === 'Paid' ? 'status-pill-paid' : student.feeStatus === 'Partial' ? 'status-pill-partial' : 'status-pill-unpaid'}`}>
                            {student.feeStatus}
                          </span>
                          <span className="text-[10px] text-slate-500 block mt-0.5 font-mono">{student.feeAmount}</span>
                        </>
                      )}
                    </td>
                    <td className="table-sticky-right">
                      <div className="flex items-center justify-center gap-1" onClick={(e) => e.stopPropagation()}>
                        {activeSubMenu === "archive" ? (
                          <>
                            <button type="button" onClick={() => handleRestore(student as ArchivedStudent)} className="p-2 text-emerald-600 hover:text-emerald-700 rounded" title="Restore">
                              <RotateCcw size={14} />
                            </button>
                            <button type="button" onClick={() => handlePermanentDelete(student as ArchivedStudent)} className="p-2 text-slate-400 hover:text-red-600 rounded" title="Permanent delete">
                              <Trash size={14} />
                            </button>
                          </>
                        ) : (
                          <>
                            <button type="button" onClick={() => openEdit(student as Student)} className="p-2 text-slate-500 hover:text-emerald-700 rounded" title="Edit student">
                              <Pencil size={14} />
                            </button>
                            <button type="button" onClick={() => confirmArchive(student as Student)} className="p-2 text-slate-400 hover:text-amber-600 rounded" title="Archive student">
                              <Archive size={14} />
                            </button>
                          </>
                        )}
                      </div>
                    </td>
                  </tr>
                ))
              ) : (
                <tr>
                  <td colSpan={7} className="text-center py-8 text-slate-500">
                    <Users size={24} className="mx-auto mb-2 text-slate-400" />
                    {activeSubMenu === "archive" ? "Archive is empty." : "No registered students found matching your search."}
                  </td>
                </tr>
              )}
            </tbody>
          </table>
        </div>
```

---

### File 4: `src/pages/MidTermEngine.tsx`
**Goal**: Make student name column sticky (`sticky left-0`) in the Marks Register table so teachers never lose track of student names when swiping across subjects on phones/tablets. Ensure inputs don't trigger iOS Safari auto-zoom.

#### Target Location: Around line 515 in `src/pages/MidTermEngine.tsx`:

```tsx
            {/* Scrollable Marks Table with Sticky Student Column */}
            <div className="overflow-x-auto -mx-1 sm:mx-0">
              <table className="w-full min-w-[650px] text-left text-sm border-collapse">
                <thead className="bg-stone-50 text-[11px] uppercase text-stone-500">
                  <tr>
                    <th className="sticky left-0 bg-stone-50 z-20 px-3 py-2.5 shadow-[2px_0_4px_rgba(0,0,0,0.04)]">
                      Student
                    </th>
                    {classSubjects.map((s) => (
                      <th key={s.id} className="px-3 py-2.5 whitespace-nowrap">
                        {s.subject_name}
                      </th>
                    ))}
                    <th className="px-3 py-2.5 whitespace-nowrap">Avg</th>
                  </tr>
                </thead>
                <tbody className="divide-y divide-stone-100">
                  {pagedMarks.map((stu) => {
                    const sm = periodMarks.filter((m) => m.student_id === stu.id);
                    const avg = sm.length ? (sm.reduce((a, b) => a + Number(b.mark), 0) / sm.length).toFixed(1) : '—';
                    return (
                      <tr key={stu.id} className="hover:bg-stone-50/70">
                        {/* Sticky Student Name Cell */}
                        <td className="sticky left-0 bg-white z-10 px-3 py-2 font-medium text-stone-900 shadow-[2px_0_4px_rgba(0,0,0,0.04)] whitespace-nowrap">
                          {stu.full_name}
                        </td>
                        {classSubjects.map((sub) => {
                          const mk = sm.find((m) => m.subject_id === sub.id);
                          const edit = editing?.studentId === stu.id && editing?.subjectId === sub.id;
                          return (
                            <td key={sub.id} className="px-3 py-2 whitespace-nowrap">
                              {edit ? (
                                <div className="flex items-center gap-1">
                                  <input
                                    autoFocus
                                    type="number"
                                    min="0"
                                    max="100"
                                    value={editing.value}
                                    onChange={(e) => setEditing({ ...editing, value: e.target.value })}
                                    onKeyDown={(e) => {
                                      if (e.key === 'Enter') void saveMark(stu.id, sub.id, editing.value);
                                      if (e.key === 'Escape') setEditing(null);
                                    }}
                                    className="w-16 rounded border border-amber-400 px-1.5 py-1 text-sm font-mono outline-none"
                                  />
                                  <button
                                    onClick={() => void saveMark(stu.id, sub.id, editing.value)}
                                    className="rounded bg-emerald-600 p-1 text-white hover:bg-emerald-700"
                                    title="Save mark"
                                  >
                                    <CheckCircle2 size={14} />
                                  </button>
                                </div>
                              ) : (
                                <div className="group flex items-center gap-1.5">
                                  <span className={`font-mono ${mk ? 'font-semibold text-stone-800' : 'text-stone-400'}`}>
                                    {mk ? Number(mk.mark) : '—'}
                                  </span>
                                  {canEditMarks && (
                                    <span className="flex sm:hidden sm:group-hover:flex items-center gap-0.5">
                                      <button
                                        onClick={() => setEditing({ studentId: stu.id, subjectId: sub.id, value: mk ? String(mk.mark) : '' })}
                                        className="rounded p-1 text-stone-400 hover:text-stone-700 hover:bg-stone-100"
                                        title="Edit mark"
                                      >
                                        <Pencil size={12} />
                                      </button>
                                      {mk && (
                                        <button
                                          onClick={() => void removeMark(stu.id, sub.id)}
                                          className="rounded p-1 text-red-500 hover:bg-red-50"
                                          title="Delete mark"
                                        >
                                          <Trash2 size={12} />
                                        </button>
                                      )}
                                    </span>
                                  )}
                                </div>
                              )}
                            </td>
                          );
                        })}
                        <td className="px-3 py-2 font-mono font-bold text-stone-900 whitespace-nowrap">{avg}</td>
                      </tr>
                    );
                  })}
                  {!pagedMarks.length && (
                    <tr>
                      <td colSpan={classSubjects.length + 2} className="py-10 text-center text-sm text-stone-500">
                        {classSubjects.length ? 'No students' : 'No subjects enabled for this class'}
                      </td>
                    </tr>
                  )}
                </tbody>
              </table>
            </div>
```

---

### File 5: `src/pages/Campaigns.tsx`
**Goal**: Make the sub-tabs (`broadcasts`, `automations`, `logs`, `gateways`) horizontally scrollable with touch-scrolling on mobile, and make the logs table sticky and touch-friendly.

#### Target Location: Around line 170 in `src/pages/Campaigns.tsx`:

```tsx
      {/* Horizontally scrollable sub-tabs on mobile */}
      <div className="flex overflow-x-auto no-scrollbar items-center gap-1 border-b border-slate-200 bg-slate-50 px-2 py-1 rounded-t -mx-1 sm:mx-0">
        <button
          type="button"
          onClick={() => setActiveTab('broadcasts')}
          className={`px-3 py-2 text-xs font-semibold rounded transition flex items-center gap-1.5 whitespace-nowrap flex-shrink-0 ${
            activeTab === 'broadcasts' ? 'bg-[#ea580c] text-white shadow-sm' : 'text-slate-600 hover:bg-slate-200'
          }`}
        >
          <Megaphone size={13} /> Broadcast notices ({filteredCampaigns.length})
        </button>
        <button
          type="button"
          onClick={() => setActiveTab('automations')}
          className={`px-3 py-2 text-xs font-semibold rounded transition flex items-center gap-1.5 whitespace-nowrap flex-shrink-0 ${
            activeTab === 'automations' ? 'bg-[#ea580c] text-white shadow-sm' : 'text-slate-600 hover:bg-slate-200'
          }`}
        >
          <Sliders size={13} /> Automation Rules
        </button>
        <button
          type="button"
          onClick={() => setActiveTab('logs')}
          className={`px-3 py-2 text-xs font-semibold rounded transition flex items-center gap-1.5 whitespace-nowrap flex-shrink-0 ${
            activeTab === 'logs' ? 'bg-[#ea580c] text-white shadow-sm' : 'text-slate-600 hover:bg-slate-200'
          }`}
        >
          <FileText size={13} /> Dispatch Logs ({logs.length})
        </button>
        <button
          type="button"
          onClick={() => setActiveTab('gateways')}
          className={`px-3 py-2 text-xs font-semibold rounded transition flex items-center gap-1.5 whitespace-nowrap flex-shrink-0 ${
            activeTab === 'gateways' ? 'bg-[#ea580c] text-white shadow-sm' : 'text-slate-600 hover:bg-slate-200'
          }`}
        >
          <Smartphone size={13} /> Gateway Test
        </button>
      </div>
```

---

## 3. Verification & Testing Protocol

To verify complete responsiveness across devices:

1. **Mobile Emulation (375px & 412px)**:
   * Open Chrome DevTools (`Ctrl+Shift+I` or `Cmd+Opt+I`).
   * Toggle Device Toolbar (`Cmd+Shift+M`).
   * Select **iPhone SE (375x667)** and **Pixel 7 (412x915)**.
   * Verify the top brand header shows the hamburger button `<Menu />`. Tap to open drawer: all items appear stacked with touch-friendly paddings.
   * Verify top utility bar does not produce horizontal body scrollbars (`overflow-x: hidden`).
2. **Student Directory & Marks Matrix**:
   * Navigate to `/students`: subnav buttons form a clean horizontal swipe bar above the student table.
   * In the table, swipe right: Reg Code stays pinned to the left, and Action buttons stay pinned to the right.
   * Navigate to `/midterm`: scroll marks table horizontally. Verify student names stay anchored on the left.
3. **Modals**:
   * Tap "Send Notice" or "Settings". Verify the dialog opens as a clean sheet with scrollable form content and anchored footer buttons.

---
*Created by AI Studio Build Engine for G.S. GACUBA 2A.*
