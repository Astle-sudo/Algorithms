<script>
    import P5 from 'p5-svelte';
    import 'C:/Users/HP/Pokedex/src/app.css';

	const sketch = (p5) => {

        let emitter;

        const Emitter = (x,y) => {
            let molecules = [];
            let position = p5.createVector(x,y);
  
            function Emit (num) {
                for (let i=0; i < num; i++) {
                    molecules.push(Particle(position.x,position.y));
                }
                Update();
                Remove();
            }
  
            function Update () {
                for (let molecule of molecules) {
                    let gravity = p5.createVector(0,0.1);
                    molecule.applyforce(gravity);
                    molecule.draw();
                    molecule.Update();
                }
            }
  
            function Remove () {
                for (let i = molecules.length-1; i >=0; i--) {
                    if (molecules[i].finish()) {
                        molecules.splice(i,1);
                    }
                }
            }
  
            return {Emit,position};
        }

        const Particle = (x,y) => {
            let pos = p5.createVector(x,y);
            let Acc = p5.createVector();
            let velocity = p5.Vector.random2D();
            velocity.mult(p5.random(-1,1));
            let lifetime = 255;
  
            function applyforce (force) {
                Acc = force;
            }
  
            function Update () {
                velocity.add(Acc);
                pos.add(velocity);
                lifetime -= 3;
            }
  
            function finish () {
                return (lifetime == 0);
            }
  
            function draw () {
                p5.stroke(lifetime);
                p5.fill(lifetime);
                p5.strokeWeight(4);
                p5.ellipse(pos.x,pos.y,4);
            }
  
            return {pos,Update,draw,applyforce,finish};
        }

		p5.setup = () => {
			p5.createCanvas(1200, 500);
            emitter = Emitter(0,0);
		};

		p5.draw = () => {
            p5.background(51);

            emitter.position.x = p5.mouseX;
            emitter.position.y = p5.mouseY;
            emitter.Emit(1);
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