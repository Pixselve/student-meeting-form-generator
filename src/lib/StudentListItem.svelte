<div class='shadow-md bg-white rounded-xl p-6 flex justify-between items-center border'>
	<div>
		<div>{report.date.toDate().toLocaleDateString()}</div>
		<div class='font-bold text-xl'>{report.studentName}</div>
	</div>
	<button disabled={loading} on:click={generateAndDownloadPDF}
					class='transition ease-in-out h-14 w-14 hover:bg-blue-600 hover:text-white block bg-gray-300 rounded-xl flex justify-center items-center text-xl'>
		<svg class:animate-bounce={loading} xmlns='http://www.w3.org/2000/svg' class='h-1/2 w-1/2' fill='none'
				 viewBox='0 0 24 24' stroke='currentColor'>
			<path stroke-linecap='round' stroke-linejoin='round' stroke-width='2'
						d='M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4' />
		</svg>

	</button>
</div>

<script lang='ts'>
import { PDFDocument } from 'pdf-lib';

let loading = false;
export let report;

async function generateAndDownloadPDF() {
	try {
		loading = true;
		const existingPdfBytes = await fetch('http://localhost:3000/rendez-vous-de-parents.pdf').then(res => res.arrayBuffer());

		const pdfDoc = await PDFDocument.load(existingPdfBytes);

		const formFieldIDs = {
			date: 'DATE',
			grade: 'classes',
			studentName: 'ELEVE',
			teacherName: 'Champ texte0',
			meetingBy: {
				parents: 'parents',
				teacher: 'enseignant',
				other: 'autre'
			},
			peoplePresent: {
				father: 'PAPA',
				mother: 'MAMAN',
				student: 'ELEVEB',
				principal: 'DIRECTEUR',
				other: 'AUTRES',
				otherText: 'PARTENAIRE'
			},
			objectives: {
				learning: 'aprentissage',
				jobs: 'metier',
				relationships: 'relations',
				adaptations: 'adapattion',
				projects: 'preojet'
			},
			details: 'POINTS ABORDES',
			importantInfos: 'INFOS'
		};

		const form = pdfDoc.getForm();

		const formFields = {
			date: form.getTextField(formFieldIDs.date),
			grade: form.getTextField(formFieldIDs.grade),
			studentName: form.getTextField(formFieldIDs.studentName),
			teacherName: form.getTextField(formFieldIDs.teacherName),
			meetingBy: {
				parents: form.getCheckBox(formFieldIDs.meetingBy.parents),
				teacher: form.getCheckBox(formFieldIDs.meetingBy.teacher),
				other: form.getCheckBox(formFieldIDs.meetingBy.other)
			},
			peoplePresent: {
				father: form.getCheckBox(formFieldIDs.peoplePresent.father),
				mother: form.getCheckBox(formFieldIDs.peoplePresent.mother),
				student: form.getCheckBox(formFieldIDs.peoplePresent.student),
				principal: form.getCheckBox(formFieldIDs.peoplePresent.principal),
				other: form.getCheckBox(formFieldIDs.peoplePresent.other),
				otherText: form.getTextField(formFieldIDs.peoplePresent.otherText)
			},
			objectives: {
				learning: form.getCheckBox(formFieldIDs.objectives.learning),
				jobs: form.getCheckBox(formFieldIDs.objectives.jobs),
				relationships: form.getCheckBox(formFieldIDs.objectives.relationships),
				adaptations: form.getCheckBox(formFieldIDs.objectives.adaptations),
				projects: form.getCheckBox(formFieldIDs.objectives.projects)
			},
			details: form.getTextField(formFieldIDs.details),
			importantInfos: form.getTextField(formFieldIDs.importantInfos)
		};


		formFields.date.setText(report.date.toDate().toLocaleDateString());
		formFields.grade.setText(report.grade);
		formFields.studentName.setText(report.studentName);
		formFields.details.setText(report.details);
		formFields.importantInfos.setText(report.importantInfos);

		report.meetingBy.forEach(el => {
			switch (el) {
				case 'TEACHER':
					formFields.meetingBy.teacher.check();
					break;
				case 'PARENTS':
					formFields.meetingBy.parents.check();
					break;
				default:
					formFields.meetingBy.other.check();
					break;
			}
		});

		report.peoplePresent.forEach(el => {
			switch (el) {
				case 'STUDENT':
					formFields.peoplePresent.student.check();
					break;
				case 'FATHER':
					formFields.peoplePresent.father.check();
					break;
				case 'MOTHER':
					formFields.peoplePresent.mother.check();
					break;
				case 'PRINCIPAL':
					formFields.peoplePresent.principal.check();
					break;
				default:
					formFields.peoplePresent.other.check();
					formFields.peoplePresent.otherText.setText(el);
					break;
			}
		});

		report.objectives.forEach(el => {
			switch (el) {
				case 'LEARNING':
					formFields.objectives.learning.check();
					break;
				case 'JOBS':
					formFields.objectives.jobs.check();
					break;
				case 'RELATIONSHIPS':
					formFields.objectives.relationships.check();
					break;
				case 'ADAPTATIONS':
					formFields.objectives.adaptations.check();
					break;
				case 'PROJECTS':
					formFields.objectives.projects.check();
					break;
				default:
					break;
			}
		});


		form.flatten();

		//
		const pdfBytes = await pdfDoc.saveAsBase64({ dataUri: true });
		const a = document.createElement('a');
		a.href = pdfBytes;
		a.setAttribute('download', `Rapport ${ report.studentName } ${ report.date.toDate().toLocaleDateString() }`);
		a.click();
		a.remove();
	} catch (e) {
		console.error({ e });
	} finally {
		loading = false;
	}
}
</script>
