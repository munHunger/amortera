<script>
	import Seo from 'sk-seo';
	import { cubicInOut } from 'svelte/easing';
	import fadeScale from '$lib/intro';
	let initialLoan = undefined;
	let value = undefined;
	let salary = undefined;

	$: loanPercentage = to2Digit(initialLoan / value) * 100;
	$: mortgageAmount = calculateMortgageRate(loanPercentage, salary, initialLoan);
	/**
	 * @param {number} val
	 */
	function to2Digit(val) {
		return Math.floor(val * 100) / 100;
	}

	/**
	 * @param {number} salary
	 * @param {number} initialLoan
	 * @returns {boolean}
	 */
	function isLowSalary(salary, initialLoan) {
		const loanThreshold = salary * 12 * 4.5;

		return loanThreshold < initialLoan;
	}

	/**
	 * @param {number} initialLoan
	 */
	function getSalaryThreshold(initialLoan) {
		return initialLoan / 12 / 4.5;
	}

	/**
	 * Calculates the mortgage rate based on loan percentage, salary, and initial loan amount.
	 *
	 * The mortgage rate is determined by the following criteria:
	 * - If the annual salary multiplied by 4.5 exceeds the initial loan amount, an additional 0.01 is added to the base rate.
	 * - The base rate is determined by the loan percentage:
	 *   - If the loan percentage is greater than 70%, the base rate is 0.02.
	 *   - If the loan percentage is greater than 50% but not more than 70%, the base rate is 0.01.
	 *   - If the loan percentage is 50% or less, the base rate is 0.
	 *
	 * @param {number} loanPercentage - The percentage of the loan.
	 * @param {number} salary - The annual salary of the borrower.
	 * @param {number} initialLoan - The initial loan amount.
	 * @returns {number} The calculated mortgage rate.
	 */
	function calculateMortgageRate(loanPercentage, salary, initialLoan) {
		const baseRate = loanPercentage > 70 ? 0.02 : loanPercentage > 50 ? 0.01 : 0;

		if (isLowSalary(salary, initialLoan)) {
			return baseRate + 0.01;
		}

		return baseRate;
	}
	const title = 'Amorterings kalkylator';
</script>

<svelte:head>
	<title>{title}</title>
</svelte:head>
<Seo
	{title}
	description="Kalkylator för att räkna ut hur mycket du ska betala i månaden i amortering"
	keywords="amortering, räkna, lån, online, kalkylator"
/>
<main>
    <h1 class="text-3xl text-accent mb-12">Räkna på din amorteringskostnad per månad</h1>
	<div>
		<p>
			Om du har tagit ett lån på <input
				type="number"
				bind:value={initialLoan}
				placeholder="lån"
				class="input w-full max-w-32 text-secondary"
			/>kr.
		</p>
		<p>
			Och du tjänar <input
				type="number"
				bind:value={salary}
				placeholder="lön"
				class="input w-full max-w-32 text-secondary"
			/>kr i månaden i bruttolön.
		</p>
		<p>
			Med en bostadsvärdering på <input
				type="number"
				bind:value
				placeholder="värdering"
				class="input w-full max-w-32 text-secondary"
			/>kr.
		</p>
		{#if value > 0 && initialLoan > 0 && salary > 0}
			<p>
				Så bör du betala <span class="text-accent"
					>{Math.floor((mortgageAmount * initialLoan) / 12)}</span
				>
				per månad, vilket motsvarar <span class="text-accent">{mortgageAmount * 100}%</span> av bostadens
				värde.
			</p>
		{/if}
	</div>

	{#if value > 0 && initialLoan > 0 && salary > 0}
		<div>
			Banken äger <span class="text-accent">{loanPercentage}%</span> av din bostad.
		</div>
	{/if}
	<div class="flex gap-2 mt-12 flex-col md:flex-row">
		{#if isLowSalary(salary, initialLoan)}
			<div
				class="card bg-neutral text-neutral-content w-96"
				transition:fadeScale={{
					delay: 0,
					duration: 200,
					easing: cubicInOut,
					baseScale: 0.5
				}}
			>
				<div class="card-body items-center text-center">
					<h2 class="card-title">Högt lån</h2>
					<p>
						Du betalar <span class="text-accent">{Math.floor((0.01 * initialLoan) / 12)}</span>
						extra per månad pga din låga lön i förhållande till lån. Höj lönen till
						<span class="text-accent">{Math.floor(getSalaryThreshold(initialLoan))}</span>
						eller sänk lånet till <span class="text-accent">{Math.floor(salary * 4.5 * 12)}</span> för
						att sänka amorteringen
					</p>
				</div>
			</div>
		{/if}
		{#if loanPercentage > 70}
			<div
				class="card bg-neutral text-neutral-content w-96"
				transition:fadeScale={{
					delay: 0,
					duration: 200,
					easing: cubicInOut,
					baseScale: 0.5
				}}
			>
				<div class="card-body items-center text-center">
					<h2 class="card-title">Låg insats</h2>
					<p>
						Du betalar <span class="text-accent">{Math.floor((0.01 * initialLoan) / 12)}</span>
						extra per månad pga banken äger mer än 70% av bostaden. Sänk lånet
						med <span class="text-accent">{initialLoan-Math.floor(value * 0.7)}</span> till <span class="text-accent">{Math.floor(value * 0.7)}</span> för att sänka amorteringen till just <span class="text-accent">{Math.floor((0.01 * initialLoan) / 12)}</span>
					</p>
				</div>
			</div>
		{/if}
	</div>
</main>
<footer class="footer footer-center bg-base-300 text-base-content p-4">
	<aside>
		<p>Denna kalkylator är ett hobbyprojekt och resultaten kan vara felaktiga. Använd på egen risk.</p>
	</aside>
</footer>

<style lang="postcss">
	p {
		@apply py-2;
	}
	input {
		@apply mx-1;
	}
	main {
		@apply flex items-center justify-center min-h-screen text-center flex-col;
	}
</style>
