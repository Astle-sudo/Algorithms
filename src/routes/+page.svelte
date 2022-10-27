<script>
    import P5 from 'p5-svelte';
    import "../app.css"

	const sketch = (p5) => {

        let numberOfPoints = 20;
        let numberOfMeans = 2;
        let Means = [];
        let points = [];

        function Clustering (points,Means) {
            for (let point of points) {
                let distances = [];
                for (let mean of Means) {
                    distances.push(p5.dist(point[0],point[1],mean.pos.x,mean.pos.y));
                }
                for (let mean of Means) {
                    if (Math.min(...distances) === p5.dist(point[0],point[1],mean.pos.x,mean.pos.y)) {
                        mean.cluster.push(point);
                    }
                }
            }
        }

        function centroid (pointsList) {
            let meanX = 0;
            let meanY = 0;
            let n = pointsList.length;
            for (let point of pointsList) {
                meanX += point[0]
                meanY += point[1]
            }
            return [meanX/n,meanY/n]
        }

        const Mean = (x,y) => {
            let pos = p5.createVector(x,y);
            let cluster = []
            let color = [p5.random(255),p5.random(255),p5.random(255)];
  
            function draw () {
                p5.push();
                p5.fill(color[0],color[1],color[2]);
                p5.square(pos.x,pos.y,10);
                p5.pop();
            }

            return {pos,cluster,draw,color};
        }

		p5.setup = () => {
			p5.createCanvas(1200, 500);
            for (let i = 0; i < numberOfMeans; i++) {
                Means.push(Mean(p5.random(p5.width),p5.random(p5.height)));
            }
            for (let i=0; i < numberOfPoints; i++) {
                points.push([p5.random(p5.width),p5.random(p5.height)]);
            }
		};

		p5.draw = () => {
            p5.background(51);
            Clustering(points,Means);
            for (let mean of Means) {
                let newMean = centroid(mean.cluster);
                mean.pos.x = newMean[0];
                mean.pos.y = newMean[1];
                mean.draw();
            for (let point of mean.cluster) {
                p5.fill(mean.color[0],mean.color[1],mean.color[2]);
                p5.circle(point[0],point[1],8);
                }
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