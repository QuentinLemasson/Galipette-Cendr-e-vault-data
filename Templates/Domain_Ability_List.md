<%*
const dv = this.app.plugins.plugins["dataview"].api;
const title = tp.file.title;

const query = `TABLE description AS Description , type AS Type, statut AS Statut FROM "Remote-Wiki/Compétences" WHERE contains(domaine,[[${title}]]) SORT file.name ASC`;
const result = await dv.queryMarkdown(query);

let tableContent = '';
if ( result.successful ) {
	tableContent = result.value
} else {
	tableContent = "~~~~\n" + result.error + "\n~~~~"
}

const startMarker = "<!-- START-ABILITY-TABLE -->\n"; 
const endMarker = "<!-- END-ABILITY-TABLE -->"; 

let content = tp.file.content;
let newContent = '';
const regex = new RegExp(`${startMarker}[\\s\\S]*?${endMarker}`); 
const newTableSection = `${startMarker}\n${tableContent}\n${endMarker}`; 

if (regex.test(content)) { 
	newContent = content.replace(regex, newTableSection); 
} else { 
	newContent = content + `\n${newTableSection}`; 
} 

app.vault.modify(app.workspace.getActiveFile(), newContent)
%>