<template>
    <svg preserveAspectRatio="xMidYMid meet" viewBox="0 0 800 650" class="svg-content-responsive">
        <g :transform="style">
            <!-- <g>
                <path class="link" v-for="b in getLinks" :key="b.id" />
            </g> -->
            <g>
                <text class="node" v-for="d in getNodes" :key="d.id" :dy="d.dy" :text-anchor="d.textAnchor" :transform="d.transform">{{ d.text }}</text>
            </g>
        </g>
    </svg>
</template>

<script>
import * as d3 from "d3";
export default {
    data() {
        return {
            data: [],
            diameter: 750
        };
    },
    mounted() {
        this.fetchData();
    },
    methods: {
        async fetchData() {
            let data = await d3.json("./flare.json");
            this.data = data;
        },
        drawNodes() {
            var root = this.packageHierarchy(this.data);
            if (root != undefined) {
                this.cluster(root);
                return root.leaves().map((d, i) => {
                    let textAnchor = d.x < 180 ? "start" : "end";
                    let transform = "rotate(" + (d.x - 90) + ")translate(" + (d.y + 8) + ",0)" + (d.x < 180 ? "" : "rotate(180)");
                    return {
                        id: i + 1,
                        dy: "0.28em",
                        text: d.data.key.split("@")[0],
                        textAnchor: textAnchor,
                        transform: transform
                    };
                });
            }
        },
        drawLinks() {
            var root = this.packageHierarchy(this.data);
            if (root != undefined) {
                return this.packageImports(root.leaves()).map((d, i) => {
                    return {
                        id: i + 1
                    };
                });
            }
        },
        packageHierarchy(data) {
            data = JSON.parse(JSON.stringify(data));
            console.log(data);
            let map = {};
            function find(name, data) {
                var node = map[name],
                    i;
                if (!node) {
                    node = map[name] = data || { name: name, children: [] };
                    if (name.length) {
                        node.parent = find(name.substring(0, (i = name.lastIndexOf("."))));
                        node.parent.children.push(node);
                        node.key = name.substring(i + 1);
                    }
                }
                return node;
            }

            data.forEach(function(d) {
                find(d.name, d);
            });

            if (map[""] != undefined) {
                return d3.hierarchy(map[""]);
            }
        },
        packageImports(nodes) {
            var map = {},
                imports = [];
            // Compute a map from name to node.
            nodes.forEach(function(d) {
                map[d.data.name] = d;
            });

            // For each import, construct a link from the source to target node.
            nodes.forEach(function(d) {
                if (d.data.imports)
                    d.data.imports.forEach(function(i) {
                        imports.push(map[d.data.name].path(map[i]));
                    });
            });

            return imports;
        }
    },
    computed: {
        cluster() {
            return d3.cluster().size([360, this.getInnerRadius]);
        },
        getNodes() {
            return this.drawNodes();
        },
        getLinks() {
            return this.drawLinks();
        },
        style() {
            return `translate(400,325)`;
        },
        getRadius() {
            return this.diameter / 2;
        },
        getInnerRadius() {
            return this.getRadius - 150;
        }
    }
};
</script>

<style>
/* svg {
    background-color: lightskyblue;
} */

.node {
    font: 300 9px "Helvetica Neue", Helvetica, Arial, sans-serif;
    fill: #000;
}
.node:hover {
    fill: #000;
}

.link {
    stroke: steelblue;
    stroke-opacity: 0.4;
    fill: none;
    /* pointer-events: none; */
}

.node:hover,
.node--source,
.node--target {
    font-weight: 700;
}

.node--source {
    fill: #2ca02c;
}

.node--target {
    fill: #d62728;
}

.link--source,
.link--target {
    stroke-opacity: 1;
    stroke-width: 2px;
}

.link--source {
    stroke: #d62728;
}

.link--target {
    stroke: #2ca02c;
}
</style>
