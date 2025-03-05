# Firehose
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Animation</title>
    <style>
        :root {
            --orange: #F68513;
        }

        html {
            box-sizing: border-box;
        }

        *, *:before, *:after {
            box-sizing: inherit;
        }

        html, body {
            width: 100%;
            height: 100%;
        }

        body {
            display: flex;
            align-items: center;
            justify-content: center;
            background: #181818;
            color: #010005;
            font-family: neue-haas-grotesk, "Helvetica Neue", Helvetica, sans-serif !important;
            font-weight: 600;
            font-style: normal;
            font-size: 14px;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            overflow: hidden;
        }

        .stage {
            position: relative;
            min-width: 716px;
            height: 1008px;
            background-color: var(--orange);
            visibility: hidden;
            overflow: hidden;
            cursor: pointer;
        }

        svg {
            position: absolute;
            width: 716px;
            height: 1008px;
        }

        .dark-patch {
            visibility: hidden;
        }

        .ball {
            visibility: hidden;
        }

        .content {
            position: relative;
            z-index: 1;
            order: 1;
            padding: 0 19px;
        }

        h1.band {
            position: absolute;
            bottom: 125px;
            left: 18px;
            font-size: 115px;
            margin: 0;
            letter-spacing: -0.5px;
            overflow: hidden;
            white-space: nowrap;
            font-weight: 600;
            line-height: 0.8;
        }

        .details {
            position: absolute;
            bottom: 29px;
            left: 26px;
            display: flex;
        }

        .date {
            padding-right: 49px;
        }

        .location {
            padding-right: 106px;
        }
    </style>
</head>
<body>
    <div class="stage">
        <svg class="svg" viewBox="0 0 716 1008" xmlns="http://www.w3.org/2000/svg">
            <rect class="dark-patch" width="100" height="150" x="616" fill="#010005" />
            <g class="square-frame">
                <g class="square">
                    <rect class="square__large" width="868" height="868" fill="#010005" />
                    <rect class="square__small" x="303" y="303" width="262" height="262" fill="#F68513" />
                    <circle class="ball" cx="405" cy="463" r="103" fill="#010005" />
                </g>
            </g>
        </svg>
        <h1 class="band">firehose</h1>
        <div class="details">
            <p class="date"><span>friday</span><span>december 13 1991</span><span>7 pm start</span></p>
            <p class="location"><span>at the seahawk center</span><span>harbor college / 1111 figueroa place</span><span>wilmington, california</span></p>
            <p class="support"><span>with babyland</span><span>sense field</span><span>spinning ceilings</span></p>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.10.0/gsap.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.10.0/CustomEase.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/SplitText/3.0.0/SplitText.min.js"></script>

    <script>
        const select = e => document.querySelector(e);
        const selectAll = e => document.querySelectorAll(e);

        const stage = select('.stage');
        const square = select('.square');
        const squareFrame = select('.square__frame');
        const global = gsap.timeline({ delay: 0.5 });
        const loopTime = 16;
        const ballDuration = loopTime / 8;
        const introRotTime = 1.3;
        const introRotDelay = 0.5;
        const squareDelay = introRotTime + introRotDelay;
        const bandST = new SplitText('.band', { type: "chars", charsClass: "bandChar", position: "absolute" });

        CustomBounce.create("ballBounce", { strength: 0.5 });

        const constEase = CustomEase.create("custom",
            `M0,0,
            C0.1,0.025,
            0.125,0.225,
            0.25,0.25,
            0.375,0.275,
            0.375,0.475,
            0.5,0.5,
            0.625,0.525,
            0.625,0.725,
            0.75,0.75,
            0.875,0.775,
            0.875,0.975,
            1,1`);

        function introAnimation() {
            let tl = gsap.timeline();
            tl.from('.square__large', {
                scaleX: 0,
                duration: 2,
                ease: 'power4.inOut',
                transformOrigin: "right center"
            }, 0)
                .to('.square-frame', {
                    rotation: 16,
                    duration: introRotTime,
                    ease: CustomEase.create("custom", "M0,0,C0.234,0.528,0.524,0.828,1,1")
                }, introRotDelay)
                .from('.square__small', {
                    scaleX: 0,
                    duration: 2,
                    ease: 'power4',
                    transformOrigin: "right center"
                }, 1.1)
                .set(['.ball', '.dark-patch'], { visibility: 'visible' }, 1.3)
                .fromTo('.ball', {
                    x: -206,
                }, {
                    x: 58,
                    ease: 'ballBounce',
                    duration: 2
                }, 1.3)

            return tl;
        }

        function loopSquare() {
            let tl = gsap.timeline({
                repeat: -1
            });
            tl.to(square, {
                duration: loopTime,
                ease: constEase,
                rotation: "+=360",
                transformOrigin: "center center"
            })
            return tl;
        }

        function loopBall() {
            let tl = gsap.timeline({
                repeat: -1,
                repeatDelay: ballDuration,
                defaults: {
                    ease: 'ballBounce',
                    duration: ballDuration
                }
            });

            tl.to('.ball', {
                y: -58
            })
                .to('.ball', {
                    x: 0
                }, loopTime / 4)
                .to('.ball', {
                    y: 0
                }, loopTime / 2)
                .to('.ball', {
                    x: 58
                }, loopTime * 0.75)

            return tl;
        }

        function animInContent() {
            let tl = gsap.timeline({
                defaults: {
                    ease: 'power2',
                    duration: 2
                }
            });

            tl.from('.bandChar', {
                y: -120,
                stagger: 0.05
            })
                .from('span', {
                    alpha: 0,
                    y: 40,
                    stagger: 0.1,
                }, 0.8);

            return tl;
        }

        function animate() {
            gsap.set(stage, {
                autoAlpha: 1
            });
            gsap.set('.square-frame', {
                transformOrigin: "center center",
                x: -141,
                y: -217
       
