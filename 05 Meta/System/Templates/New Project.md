<%*
// Driver template — scaffolds a new project (folder + assets/ + hub note + .base file with icons).
//
// Invoke via Command Palette → "Templater: Create new note from template" → "New Project".
// (Do NOT use "Open Insert Template modal" — that requires an active editor.)
//
// Only prompts for the project name. Icons + colors are baked in:
//   - Hub note   → LiFilePenLine,   #0dbd00 (green)
//   - Board      → LiSquareKanban,  #650094 (purple)
//   - assets/    → LiImage,         #cc0000 (red)
//   - outputs/   → LiPencilRuler,   #e8a317 (amber)

const HUB_ICON     = { iconName: "LiFilePenLine",  iconColor: "#0dbd00" };
const BOARD_ICON   = { iconName: "LiSquareKanban", iconColor: "#650094" };
const ASSETS_ICON  = { iconName: "LiImage",        iconColor: "#cc0000" };
const OUTPUTS_ICON = { iconName: "LiPencilRuler",  iconColor: "#e8a317" };

// First thing: hide the driver-instance file Templater just created in the vault root.
// Rename it to a hidden temp name in 05 Meta/System/ so the user never sees it flash in root.
// The cleanup() at the end then deletes it via the same TFile reference (the rename mutates the path).
if (tp.config.target_file) {
  try {
    const tempPath = `05 Meta/System/.new-project-driver-${Date.now()}.md`;
    await app.fileManager.renameFile(tp.config.target_file, tempPath);
  } catch (e) { /* if rename fails, cleanup still works from original path */ }
}

const cleanup = async () => {
  if (tp.config.target_file) {
    try { await app.vault.delete(tp.config.target_file); } catch (e) {}
  }
};

const projectName = await tp.system.prompt("Project name");
if (!projectName) { await cleanup(); return; }

// --- Folder structure ---
const projectFolder = `01 Projects/${projectName}`;
if (!app.vault.getAbstractFileByPath(projectFolder)) {
  await app.vault.createFolder(projectFolder);
}
const folder = app.vault.getAbstractFileByPath(projectFolder);

const assetsPath = `${projectFolder}/assets`;
if (!app.vault.getAbstractFileByPath(assetsPath)) {
  await app.vault.createFolder(assetsPath);
}

const outputsPath = `${projectFolder}/outputs`;
if (!app.vault.getAbstractFileByPath(outputsPath)) {
  await app.vault.createFolder(outputsPath);
}

// --- Resolve templates ---
const hubTemplate = tp.file.find_tfile("05 Meta/System/Templates/Project.md");
const baseTemplate = tp.file.find_tfile("05 Meta/System/Templates/Project.base");

if (!hubTemplate || !baseTemplate) {
  new Notice("New Project: template files not found in 05 Meta/System/Templates/");
  await cleanup();
  return;
}

// --- Create the project board (don't open) ---
await tp.file.create_new(baseTemplate, projectName, false, folder);

// --- Create the project hub note (open it) ---
const hubFile = await tp.file.create_new(hubTemplate, projectName, true, folder);

// --- All three icons via icon-folder plugin data.json ---
// One mechanism for hub + board + assets, so behaviour is uniform.
// Order matters: disable FIRST (plugin saves its in-memory state to data.json),
// THEN write our entries on top, THEN enable (plugin reads data.json fresh).
const hubPath = `${projectFolder}/${projectName}.md`;
const basePath = `${projectFolder}/${projectName}.base`;
const iconDataPath = '.obsidian/plugins/obsidian-icon-folder/data.json';
try {
  // 1. Disable — plugin flushes in-memory state to data.json
  await app.plugins.disablePlugin('obsidian-icon-folder');

  // 2. Write our entries on top of the now-flushed data.json
  const raw = await app.vault.adapter.read(iconDataPath);
  const data = JSON.parse(raw);
  data[hubPath]     = HUB_ICON;
  data[basePath]    = BOARD_ICON;
  data[assetsPath]  = ASSETS_ICON;
  data[outputsPath] = OUTPUTS_ICON;
  await app.vault.adapter.write(iconDataPath, JSON.stringify(data, null, 2));

  // 3. Enable — plugin reads the updated data.json, icons render
  await app.plugins.enablePlugin('obsidian-icon-folder');
} catch (e) {
  // If anything went wrong, make sure the plugin ends up enabled
  try { await app.plugins.enablePlugin('obsidian-icon-folder'); } catch (_) {}
  new Notice("Couldn't set icons: " + e.message);
}

// --- Delete the driver-instance file Templater created ---
await cleanup();

new Notice(`Project "${projectName}" scaffolded.`);
-%>
