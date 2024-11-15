<script>
	import { page } from '$app/stores';
	import ListItem from '$lib/components/ListItem.svelte';
	import { enableCache } from 'iconify-icon';

	let { data } = $props();
	let currentQuestion = $state(0);
	let selectedAnswer = '';
	let score = $state(0);
	let submitted = $state(false);

	function handleSubmit() {
		data.questions[currentQuestion].selectedAnswer = selectedAnswer;

		if (selectedAnswer === data.questions[currentQuestion].answer) {
			score++;
		}
		submitted = true;
	}

	function getNextQuestion() {
		currentQuestion++;
		submitted = false;
	}

	function getProgressValue() {
		return (currentQuestion / data.questions.length) * 100;
	}
</script>

<progress class="progress progress-primary w-56" value={getProgressValue()} max="100"></progress>

{#each data.questions as question, index (question.question)}
	{#if index === currentQuestion}
		<p>Frage {index + 1} von {data.questions.length}</p>
		<h2>{question.question}</h2>
		{#each question.options as option, optionIndex (option)}
			{#key submitted}
				<ListItem
					title={option}
					index={optionIndex}
					focusAction={() => (selectedAnswer = option)}
					correctAnswer={question.selectedAnswer && question.answer === option}
					disabled={submitted}
				/>
			{/key}
		{/each}
	{/if}
{/each}

{#if !submitted}
	<button class="btn btn-primary" onclick={handleSubmit}>Submit</button>
{:else}
	<button class="btn btn-primary" onclick={getNextQuestion}>Next Question</button>
{/if}
