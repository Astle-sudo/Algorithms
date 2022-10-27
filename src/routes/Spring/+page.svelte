<script>
    import P5 from 'p5-svelte';
    import '/src/app.css';

	const sketch = (p5) => {

        let bobs = [];
        let springs = [];
        let gravity;

        const Particle = (x,y,state) => {
            let position = p5.createVector(x,y);
            let velocity = p5.createVector(0,0);
            let Acc = p5.createVector();
  
            function applyforce (force) {
                Acc.add(force);
            }
  
            function Update () {
                if (state) {
                    velocity.add(Acc);
                    position.add(velocity);
                    velocity.mult(0.99);
                    Acc.set(0,0);
                }
            }
  
            function draw () {
                p5.stroke(255);
                p5.fill(255);
                p5.strokeWeight(4);
                p5.ellipse(position.x,position.y,16);
            }
  
            return {position,Update,draw,applyforce,state};
        }

        const Spring = (k,restlength,a,b) => {
  
            function Apply () {
                let force = p5.Vector.sub(b.position,a.position);
                let X = force.mag() - restlength;
                force.normalize();
                force.mult(k * X);
                a.applyforce(force);
                a.Update();
                force.mult(-1);
                b.applyforce(force);
                b.Update();
            }
  
            function draw () {
                p5.stroke(255);
                p5.strokeWeight(2);
                p5.line(a.position.x,a.position.y,b.position.x,b.position.y);
            }
  
            return {Apply,draw};
        }

		p5.setup = () => {
			p5.createCanvas(1200, 500);

            for (let i=0; i < 5; i++) {
                if (i == 0) {
                    let bob = Particle(p5.width/2,i * 50,false);
                    bobs.push(bob);
                }
                else {
                    let bob = Particle(p5.width/2,i * 50,true);
                    bobs.push(bob);
                }
                if (i !== 0) {
                    let spring = Spring(0.01,50,bobs[i],bobs[i-1]);
                    springs.push(spring);
                }
            }
            gravity = p5.createVector(0,0.1);
		};

		p5.draw = () => {
            p5.background(51);

            for (let spring of springs) {
                spring.Apply();
            }
  
            p5.beginShape();
            p5.stroke(255);
            p5.strokeWeight(2);
            p5.noFill();
            p5.curveVertex(bobs[0].position.x,bobs[0].position.y);
            for (let bob of bobs) {
                bob.applyforce(gravity);
                p5.curveVertex(bob.position.x,bob.position.y);
            }
            p5.curveVertex(bobs[4].position.x,bobs[4].position.y);
            p5.endShape();

  
            if (p5.mouseIsPressed) {
                bobs[bobs.length-1].position.x = p5.mouseX;
                bobs[bobs.length-1].position.y = p5.mouseY;
            }
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
