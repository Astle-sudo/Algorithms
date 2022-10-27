<script>
    import P5 from 'p5-svelte';
    import '/src/app.css';

	const sketch = (p5) => {

        let values;
        let w = 10;
        let states = [];

        async function sleep (ms) {
            return new Promise (resolve => setTimeout(resolve,ms));
        }  

        async function swap (arr,a,b) {
            await sleep (50);
            let temp = arr[a];
            arr[a] = arr[b];
            arr[b] = temp;
        }

        async function partition (arr,start,end) {
            let pivotIndex = start;
            let pivotValue = arr[end];
            states[pivotIndex] = 0;
            for (let i = start; i < end; i++) {
                if (arr[i] < pivotValue) {
                    await swap(arr,i,pivotIndex);
                    states[pivotIndex] = -1;
                    pivotIndex ++;
                    states[pivotIndex] = -1;
                }
            }
            await swap (arr,pivotIndex,end);
            return pivotIndex;
        }

        async function QuickSort (arr,start,end) {
            if (start >= end) {
                return;
            }
            let index = await partition (arr,start,end);
            states[index] = -1;
            await Promise.all([
                QuickSort (arr,start,index - 1),
                QuickSort (arr,index + 1,end)
            ])
        }

		p5.setup = () => {
			p5.createCanvas(1200, 500);
            values = new Array(p5.floor(p5.width/w));
            for (let i = 0; i < values.length; i++) {
                values[i] = p5.random(p5.height);
                states[i] = -1;
            }
            QuickSort (values,0,values.length - 1);
		};

		p5.draw = () => {
            p5.background(51);
            for (let i = 0; i < values.length; i++) {
                p5.stroke(0);
                if (states[i] == 0) {
                    p5.fill(255,0,0)
                }
                else {
                    p5.fill(255);
                }
                p5.rect(i * w, p5.height - values[i],w,values[i]);
            };  
		};

	};

</script>

<div class="flex justify-center">
    <P5 {sketch} />
</div>

<style>
    :global(body) {
        background-color: white;
    }
</style>
