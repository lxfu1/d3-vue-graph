<template>
  <div class="container">
    <div id="d3graph"></div>
    <div class="card">
     <div class="header">
      <div class="group">
        <span>公司:</span>
        <b>{{currenNode.name}}</b>
      </div>
      </div>
       <div class="group">
        <span>地址:</span>
        <b>{{currenNode.address}}</b>
      </div>
       <div class="group">
        <span>信用码:</span>
        <b>{{currenNode.credit_code}}</b>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import * as d3 from 'd3';
import { get, uniq, find } from 'lodash-es';

export default {
  msg: 'd3graph',
  data() {
    return {
      /**
      * Provide data which cannot be correctly parsed and processed.
      * Static data is temporarily used
      */
      nodes: [
        {
          id: 'n1',
          name: '台州市沃尔夫国际贸易有限公司',
          setup_time: '2006/8/23',
          address: '台州市黄岩春江苑1-1-1902室',
          captial: '56.0万人民币',
          credit_code: '91331003789652240P',
          color: '#F6BD16', // 不知道匹配规则，直接写死
        },
        {
          id: 'n2',
          name: '台州宏汇机械有限公司',
          setup_time: '2007/1/11',
          address: '玉环县汽摩工业园金汇路6号',
          captial: '777.0万人民币',
          credit_code: '91331021757055863T',
          color: '#F08BB4',
        },
        {
          id: 'n3',
          name: '中国航空技术北京有限公司',
          setup_time: '1988/8/12',
          address: '北京市北京经济技术开发区宏达北路16号6号楼',
          captial: '80000.0万人民币',
          credit_code: '91110302101114816L',
          color: '#65789B',
        },
        {
          id: 'n4',
          name: '浙江鹏顺进出口有限公司',
          setup_time: '2007/5/17',
          address: '浙江诸暨艮塔路9号银证大厦8楼',
          captial: '0万人民币',
          credit_code: '3306812004868',
          color: '#9661BC',
        },
        {
          id: 'n5',
          name: '浙江鹏顺进出口有限公司(1)',
          setup_time: '2007/5/17',
          address: '浙江诸暨艮塔路9号银证大厦8楼',
          captial: '0万人民币',
          credit_code: '3306812004868',
          color: '#9661BC',
        },
        {
          id: 'n6',
          name: '浙江鹏顺进出口有限公司(2)',
          setup_time: '2007/5/17',
          address: '浙江诸暨艮塔路9号银证大厦8楼',
          captial: '0万人民币',
          credit_code: '3306812004868',
          color: '#9661BC',
        },
        {
          id: 'n7',
          name: '象山东兴雕刻古董家具有限公司(2002)',
          setup_time: '1900/1/1',
          address: '城西路4号',
          captial: '0万人民币',
          credit_code: '3302251001312',
          color: '#7262FD',
        },
      ],
      links: [
        {
          source: 'n4',
          target: 'n1',
          type: '出口',
        },
        {
          source: 'n3',
          target: 'n1',
          type: '进口',
        },
        {
          source: 'n2',
          target: 'n1',
          type: '出口',
        },

        {
          source: 'n5',
          target: 'n6',
          type: '进口',
        },
        {
          source: 'n7',
          target: 'n6',
          type: '进口',
        },
        {
          source: 'n6',
          target: 'n4',
          type: '出口',
        },
      ],
      currenNode: {
        id: 'n1',
        name: '台州市沃尔夫国际贸易有限公司',
        setup_time: '2006/8/23',
        address: '台州市黄岩春江苑1-1-1902室',
        captial: '56.0万人民币',
        credit_code: '91331003789652240P',
        color: '#F6BD16', // 不知道匹配规则，直接写死
      },
      svg: null,
    };
  },
  mounted() {
    // this.processDara(STATISTICDATA);
    const { nodes, links } = this;
    this.forceGraph({
      nodes,
      links,
    }, {
      currenNode: this.currenNode,
      onNodeClick: this.nodeClick,
      onNodeMouseOver: this.nodeClick,
    });
  },
  methods: {
    /** Transform the sample data into the data structures required for D3, unused */
    processDara(data = []) {
      let nodes = [];
      const links = [];
      data.forEach((item) => {
        nodes.push({
          ...get(item, 'p.start.properties'),
          id: get(item, 'p.start.properties.name'),
          type: get(item, 'p.start.labels'),
        });
        links.push({
          source: get(item, 'p.start.properties.name'),
          target: get(item, 'p.end.properties.name'),
          type: get(item, 'p.segments.0.relationship.type'),
          relationship: get(item, 'p.segments.0.relationship.properties.name'),
        });
      });
      nodes = uniq(nodes);
      this.nodes = nodes;
      this.links = links;
    },
    nodeClick(node) {
      this.currenNode = node;
    },
    forceGraph({
      nodes: originNodes, // an iterable of node objects (typically [{id}, …])
      links: originLinks, // an iterable of link objects (typically [{source, target}, …])
    }, {
      onNodeClick,
      onNodeMouseOver,
      currenNode,
      nodeId = (d) => d.id, // given d in nodes, returns a unique identifier (string)
      nodeTitle = (d) => `${d.id}\n${d.group}`,
      nodeStroke = '#fff', // node stroke color
      nodeStrokeWidth = 1.5, // node stroke width, in pixels
      nodeStrokeOpacity = 0.85, // node stroke opacity
      nodeRadius = 30, // node radius, in pixels
      nodeStrength = -400,
      linkSource = ({ source }) => source, // given d in links, returns a node identifier string
      linkTarget = ({ target }) => target, // given d in links, returns a node identifier string
      linkStroke = '#999', // link stroke color
      linkStrokeOpacity = 0.6, // link stroke opacity
      linkStrokeWidth = 2, // given d in links, returns a stroke width in pixels
      linkStrokeLinecap = 'round', // link stroke linecap
      linkStrength = 0.5,
      colors = d3.schemeTableau10, // an array of color strings, for the node groups
      width = 800, // outer width, in pixels
      height = 800, // outer height, in pixels
      invalidation, // when this promise resolves, stop the simulation
    } = {}) {
      function intern(value) {
        return value !== null && typeof value === 'object' ? value.valueOf() : value;
      }
      let selectNode = currenNode;
      // Compute values.
      const N = d3.map(originNodes, nodeId).map(intern);
      const LS = d3.map(originLinks, linkSource).map(intern);
      const LT = d3.map(originLinks, linkTarget).map(intern);
      if (nodeTitle === undefined) nodeTitle = (_, i) => N[i];
      const T = nodeTitle == null ? null : d3.map(originNodes, nodeTitle);
      const W = typeof linkStrokeWidth !== 'function' ? null : d3.map(originLinks, linkStrokeWidth);

      // Replace the input nodes and links with mutable objects for the simulation.
      const nodes = d3.map(originNodes, (_, i) => ({ id: N[i] }));
      const links = d3.map(originLinks, (_, i) => ({ source: LS[i], target: LT[i], type: _.type }));

      // Construct the forces.
      const forceNode = d3.forceManyBody();
      const forceLink = d3.forceLink(links).id(({ index: i }) => N[i]);
      if (nodeStrength !== undefined) forceNode.strength(nodeStrength);
      if (linkStrength !== undefined) forceLink.strength(linkStrength);

      const svg = d3.select('#d3graph')
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .attr('viewBox', [-width / 2, -height / 2, width, height])
        .attr('style', 'max-width: 100%; height: auto; height: intrinsic;');
      this.svg = svg;
      // draw arrow marker
      const defs = svg.append('defs');
      const arrowPath = 'M2,2 L10,6 L2,10 L6,6 L2,2';
      const arrowMarker = defs.append('marker')
        .attr('id', 'arrow')
        .attr('markerUnits', 'strokeWidth')
        .attr('markerWidth', '12')
        .attr('markerHeight', '12')
        .attr('viewBox', '0 0 12 12')
        .attr('refX', nodeRadius - 6)
        .attr('refY', 6)
        .attr('orient', 'auto');
      arrowMarker.append('path')
        .attr('d', arrowPath)
        .attr('fill', linkStroke);
      // 渐变
      const radialColor = d3.scaleLinear()
        .domain([0, 1.0])
        .range(['red', 'gold']);
      defs.append('radialGradient')
        .attr('id', 'gradient')
        .attr('cx', '50%')// not really needed, since 50% is the default
        .attr('cy', '50%')// not really needed, since 50% is the default
        .selectAll('stop')
        .data([
          { offset: '0%', color: radialColor(0) },
          { offset: '20%', color: radialColor(0.2) },
          { offset: '40%', color: radialColor(0.4) },
          { offset: '60%', color: radialColor(0.6) },
          { offset: '80%', color: radialColor(0.8) },
          { offset: '100%', color: radialColor(1) },
        ])
        .enter()
        .append('stop')
        .attr('offset', (d) => d.offset)
        .attr('stop-color', (d) => d.color);
      // 连线
      const link = svg.append('g')
        .selectAll('line')
        .data(links)
        .join('line')
        .attr('stroke', linkStroke)
        .attr('stroke-opacity', linkStrokeOpacity)
        .attr('stroke-width', typeof linkStrokeWidth !== 'function' ? linkStrokeWidth : null)
        .attr('stroke-linecap', linkStrokeLinecap)
        .attr('marker-end', 'url(#arrow)');

      const linkText = svg.append('g')
        .selectAll('text')
        .data(links)
        .join('text')
        .attr('text-anchor', 'middle')
        .attr('dominant-baseline', 'middle')
        .attr('fill', 'rgba(0,0,0,.45)')
        .text((item) => item.type) // 可以通过 id 匹配任何数据
        .call(drag(simulation));

      // force layout
      const simulation = d3.forceSimulation(nodes)
        .force('link', forceLink)
        .force('charge', forceNode)
        .force('center', d3.forceCenter())
        .on('tick', ticked);
      simulation.force('link').distance(360); // 连接距离，就是边的长度
      const node = svg.append('g')
        .selectAll('circle')
        .data(nodes)
        .join('circle')
        .attr('r', nodeRadius)
        .attr('fill', (item) => find(originNodes, (n) => n.id === item.id).color)
        .attr('id', (item) => item.id)
        .attr('stroke', nodeStroke)
        .attr('stroke-opacity', nodeStrokeOpacity)
        .attr('stroke-width', nodeStrokeWidth)
        .call(drag(simulation));

      const text = svg.append('g')
        .selectAll('text')
        .data(nodes)
        .join('text')
        .attr('text-anchor', 'middle')
        .attr('dominant-baseline', 'middle')
        .attr('fill', 'rgba(0,0,0,.85)')
        .text((item) => find(originNodes, (n) => n.id === item.id).name) // 可以通过 id 匹配任何数据
        .call(drag(simulation));

      if (selectNode) {
        d3.select(`#${selectNode.id}`).attr('stroke', 'url(#gradient)').attr('stroke-width', 12);
      }

      const heightLight = (item) => {
        document.body.style.cursor = 'pointer';
        svg.select(`#${item.id}`).attr('stroke', 'url(#gradient)').attr('stroke-width', 12);
      };
      const resetStatus = (item) => {
        document.body.style.cursor = 'auto';
        svg.selectAll('circle').attr('stroke', '#fff').attr('stroke-width', nodeStrokeWidth);
        if (selectNode) {
          d3.select(`#${selectNode.id}`).attr('stroke', 'url(#gradient)').attr('stroke-width', 12);
        }
      };
      node.on('mouseover', (e, item) => {
        heightLight(item);
      });

      node.on('mouseleave', (e, item) => {
        resetStatus(item);
      });
      text.on('mouseover', (e, item) => {
        heightLight(item);
      });
      text.on('mouseleave', (e, item) => {
        resetStatus(item);
      });
      node.on('click', (e, item) => {
        const cNode = find(originNodes, (n) => n.id === item.id);
        selectNode = cNode;
        onNodeClick(cNode);
      });
      text.on('click', (e, item) => {
        const cNode = find(originNodes, (n) => n.id === item.id);
        selectNode = cNode;
        onNodeClick(cNode);
      });
      function ticked() {
        link
          .attr('x1', (d) => d.source.x)
          .attr('y1', (d) => d.source.y)
          .attr('x2', (d) => d.target.x)
          .attr('y2', (d) => d.target.y);

        node
          .attr('cx', (d) => d.x)
          .attr('cy', (d) => d.y);
        text
          .attr('x', (d) => d.x)
          .attr('y', (d) => d.y);

        linkText.attr('x', (d) => {
          const { source, target } = d;
          return (source.x + target.x) / 2;
        })
          .attr('y', (d) => {
            const { source, target } = d;
            return (source.y + target.y) / 2;
          });
      }
      if (W) link.attr('stroke-width', ({ index: i }) => W[i]);
      if (T) node.append('title').text(({ index: i }) => T[i]);
      if (invalidation != null) invalidation.then(() => simulation.stop());

      function drag(drawSimulation) {
        function dragstarted(event) {
          if (!event.active) drawSimulation.alphaTarget(0.3).restart();
          event.subject.fx = event.subject.x;
          event.subject.fy = event.subject.y;
        }

        function dragged(event) {
          event.subject.fx = event.x;
          event.subject.fy = event.y;
        }

        function dragended(event) {
          if (!event.active) drawSimulation.alphaTarget(0);
          event.subject.fx = null;
          event.subject.fy = null;
        }

        return d3.drag()
          .on('start', dragstarted)
          .on('drag', dragged)
          .on('end', dragended);
      }

      return svg.node();
    },
    initGraph() {
      console.log('init');
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.container {
  position: relative;
  height: 100%;

  .card {
    position: absolute;
    top: 0;
    right: 0;
    width: 180px;
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 16px;
    font-size: 12px;
    line-height: 1.5;

    .header {
      border-bottom: 1px solid #ccc;
    }
    .group {
      display: flex;
      flex-direction: row;
      justify-content: flex-start;

      b {
        display: block;
        flex: 1;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }
    }
  }
}
</style>
