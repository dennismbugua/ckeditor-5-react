# Professional Editor (CKEditor 5 + React)

A small, polished demo showing CKEditor 5 integrated into a modern React UI with theme support, fullscreen editing, and a professional, accessible UX.

This README explains the business value, potential use cases, and how the system is built — presented conversationally so you can quickly onboard teammates or stakeholders.

---

## Why this matters — business impact (the how and why)

Good content drives conversions, trust, and long-term organic growth. This editor is built to help product teams and content creators ship better content, faster, with lower friction.

- Faster authoring: A polished, distraction-reduced editor reduces cognitive load and increases throughput for writers and marketers.
- Better conversions: More readable, well-structured content typically results in longer user sessions and higher conversion rates. Embeddable editors help teams iterate quickly without shipping full CMS changes.
- Lower operational cost: Rich in-browser editing removes bottlenecks from engineering-heavy content updates and lowers time-to-publish.

How this demo helps your business:
- Prototype and validate content-driven features quickly.
- Use as an embeddable editor inside product pages, admin panels, or documentation tooling.
- Provide a modern editor experience (toolbar, images, media embed, tables) with accessibility and theming out of the box.

Bottom line: investing in a high-quality editing experience can measurably improve writer productivity and the quality of published content, which cascades to better SEO and customer engagement.

---

## Potential use cases

- CMS/editor integrations for marketing teams — full rich-text features without building from scratch.
- Admin dashboards where non-technical users create articles, release notes, or knowledge-base content.
- Embedded writing surfaces in applications (comments, user profiles, project wikis).
- Collaborative content starting point (integrate with a backend or real-time layer later).

---

## What this project does (high level)

- Integrates CKEditor 5 Classic build into a React app.
- Adds a polished UI wrapper: card layout, toolbar styling, a status bar (word count + save indicator), and theme support.
- Provides a Dark / Light mode toggle that persists across sessions (via localStorage).
- Implements Fullscreen editing using the browser Fullscreen API with a CSS fallback.

It is intentionally small so you can copy the components into larger apps or extend the editor config with more plugins (collaboration, comments, custom image upload adapters, etc.).

---

## Architecture & file layout

This demo follows a simple single-page layout but is structured so it can be dropped into larger apps.

- `src/index.js` — the app entry and the ProfessionalEditor component. This component handles:
	- editor initialization (onInit)
	- onChange handlers and word counting
	- status bar and save indicator
	- fullscreen handling and a wrapper ref
	- theme-aware global styles using Emotion's `Global` component

- `src/styles.css` — global styles that complement the Emotion CSS-in-JS styles (fonts, resets, scrollbar, high-contrast support).

- `public/index.html` — single HTML entry used by `react-scripts`.

This layout keeps presentation and small UI logic together for quick prototyping. In a larger app you might split `ProfessionalEditor` into smaller components (Toolbar, StatusBar, EditorFrame) and move logic into hooks.

---

## Key component contract (inputs / outputs)

ProfessionalEditor (React class component)
- Inputs (props):
	- `isDarkMode` (boolean) — optional; used to adapt internal styles.
- Outputs (events/side-effects):
	- Calls `editor.getData()` on change and updates internal state (word count and content).
	- Logs editor instance for debugging and enhances developer visibility.

Success criteria for the component:
- Maintains content state and exposes a clear onChange lifecycle.
- Responsive and accessible (focus rings, reduced-motion support).
- Fullscreen and theme toggle both work and persist where expected.

---

## How it works (selected code snippets)

Below are short, annotated snippets you can use to understand the core ideas.

1) Mounting CKEditor in React (from `src/index.js`):

```javascript
import CKEditor from '@ckeditor/ckeditor5-react';
import ClassicEditor from '@ckeditor/ckeditor5-build-classic';

<CKEditor
	editor={ClassicEditor}
	data={this.state.content}
	config={{
		toolbar: [ 'heading', '|', 'bold', 'italic', 'link', 'bulletedList', 'undo', 'redo' ]
	}}
	onInit={editor => {
		// Keep the instance for programmatic access
		this.editorInstance = editor;
	}}
	onChange={(event, editor) => {
		const data = editor.getData();
		// Update word counter and auto-save status
		this.handleEditorChange(event, editor);
	}}
/>
```

2) Dark-mode persistence (hook used in `App`):

```javascript
const [isDarkMode, setIsDarkMode] = useState(() => {
	try { return JSON.parse(localStorage.getItem('isDarkMode')) || false; } catch { return false; }
});

useEffect(() => {
	document.documentElement.setAttribute('data-theme', isDarkMode ? 'dark' : 'light');
	localStorage.setItem('isDarkMode', JSON.stringify(isDarkMode));
}, [isDarkMode]);
```

3) Fullscreen API handling (wrapper ref + listeners):

```javascript
// request fullscreen on the wrapper element
if (el.requestFullscreen) {
	el.requestFullscreen().catch(err => {
		// fallback to CSS fullscreen mode if the API fails
		this.setState({ isFullscreen: true });
	});
}

// listen for exit (ESC) and sync component state
componentDidMount() {
	document.addEventListener('fullscreenchange', this.onFullScreenChange);
}

onFullScreenChange() {
	this.setState({ isFullscreen: !!document.fullscreenElement });
}
```

---

## How to run (quick)

1. Install dependencies:

```bash
npm install
```

2. Start the dev server:

```bash
npm start
```

3. Open `http://localhost:3000` and try:
- Toggle Light / Dark (top button) — the choice will persist.
- Enter Fullscreen (editor button) — press ESC to exit.
- Type and watch the save indicator and word count update.

---

## Studies & references (selected)

Below are reputable resources that back up the business rationale for investing in a high-quality content experience. They provide context on content ROI, user expectations for UX, and the importance of editing and publishing velocity.

- Content Marketing Institute — content marketing benchmarks and best practices (great for stats about cost-per-lead and lead generation):
	https://contentmarketinginstitute.com/

- HubSpot — marketing statistics and inbound benefits (data-driven articles on inbound vs outbound and conversion impact):
	https://www.hubspot.com/marketing-statistics

- Nielsen Norman Group — UX research on readability, attention, and content structure; useful to justify editor UX investments:
	https://www.nngroup.com/articles/

- Forrester / Gartner (examples) — reports on digital experience and customer experience ROI. Vendor-specific reports require access but they consistently find that better UX reduces churn and increases conversion. See your organization's Forrester/Gartner access for precise citations.

- CKEditor project & docs — the underlying editor used in this demo:
	https://ckeditor.com/docs/

Note: If you need precise numeric citations for a pitch deck (e.g., "X% uplift from content improvements"), I can pull the latest peer-reviewed reports or industry whitepapers and add a small slide-ready appendix with exact figures and direct links.

---

## Next steps / recommendations

- Add an image upload adapter and a secure backend endpoint for media storage.
- Add persistence: sync editor content to your API (debounced auto-save + draft management).
- Add role-based access and a simple audit trail for published content.
- Extend with collaboration (Real-time) if your product needs concurrent authoring.
- Add automated E2E tests to validate the editor surface and plugin integrations.