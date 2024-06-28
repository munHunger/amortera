<script>
	let initialLoan = 3_000_000;
	let value = 3_600_000;
	let salary = 52_000;

	$: loanPercentage = to2Digit(initialLoan / value) * 100;
	$: mortgageAmount = calculateMortgageRate(loanPercentage, salary, initialLoan);
	/**
	 * @param {number} val
	 */
	function to2Digit(val) {
		return Math.floor(val * 100) / 100;
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
		const loanThreshold = salary * 12 * 4.5;
		const baseRate = loanPercentage > 70 ? 0.02 : loanPercentage > 50 ? 0.01 : 0;

		if (loanThreshold < initialLoan) {
			return baseRate + 0.01;
		}

		return baseRate;
	}
</script>

<main>
	<div>
		<p>
			Om du har tagit ett lån på <input
				type="number"
				bind:value={initialLoan}
				placeholder="kr"
				class="input w-full max-w-32"
			/>kr.
		</p>
		<p>
			Och du tjänar <input
				type="number"
				bind:value={salary}
				placeholder="kr"
				class="input w-full max-w-32"
			/>kr i månaden i bruttolön.
		</p>
		<p>
			Med en bostadsvärdering på <input
				type="number"
				bind:value
				placeholder="kr"
				class="input w-full max-w-32"
			/>kr.
		</p>
		<p>
			Så bör du betala <span class="text-accent">{mortgageAmount * initialLoan / 12}</span> per månad, vilket motsvarar <span class="text-accent">{mortgageAmount * 100}%</span> av
			bostadens värde.
		</p>
	</div>

	<div>
		Banken äger <span class="text-accent">{loanPercentage}%</span> av din bostad.
	</div>
</main>

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
