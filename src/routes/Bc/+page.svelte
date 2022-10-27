<script>
    import P5 from 'p5-svelte';
    import '/src/app.css';

	const sketch = (p5) => {

        let curveline;
        let newcurve;

        const Bezier = (startX,startY,endX,endY) => {
            let start = p5.createVector(startX,startY);
            let end = p5.createVector(endX,endY);
            let Bezier = p5.createVector();
  
            function control () {
                if (p5.mouseIsPressed) {
                    Bezier.x = p5.mouseX;
                    Bezier.y = p5.mouseY;
                }
            }
  
            function draw () {
                p5.stroke(255);
                p5.strokeWeight(4);
                p5.noFill();
                p5.beginShape();
                for (let i = 0; i < 1; i += 0.01) {
                    let x = ((1-i)*(1-i)*start.x) + 2*((1-i)*i*Bezier.x) + (i*i*end.x);
                    let y = ((1-i)*(1-i)*start.y) + 2*((1-i)*i*Bezier.y) + (i*i*end.y);
                    p5.curveVertex(x,y);
                }
                p5.endShape();
                control();
            }
            return {draw};
        }

		p5.setup = () => {
			p5.createCanvas(1200, 500);
            curveline = Bezier(p5.random(1200),p5.random(500),p5.random(1200),p5.random(500));
            newcurve = Bezier(p5.random(1200),p5.random(500),p5.random(1200),p5.random(500));
		};

		p5.draw = () => {
            p5.background(51);
            curveline.draw();
            newcurve.draw()
		};

	};

</script>

<div class="flex justify-center">
    <P5 {sketch} />
</div>
