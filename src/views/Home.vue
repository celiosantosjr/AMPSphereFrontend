<template>
  <div class="Home">
    <div class="row justify-center">
      <div class="col-0 col-xl-2 bg-white"></div>
      <div class="col-12 col-md-7 col-xl-5 justify-center q-pr-md q-ma-auto">
        <div class="row">
          <div class="col-12 text-center subsubsection-title-center q-mb-md">Overall statistics and distribution</div>
          <div class="col-12 q-pa-md">
            <q-carousel animated v-model="slideIndex" control-color="primary" control-text-color="black"
                        arrows height="25rem">
              <q-carousel-slide :name="1" class="column ">
                <q-img :src="distributionGraphs[0].image" fit="scale-down"></q-img>
              </q-carousel-slide>
              <q-carousel-slide :name="2" class="column ">
                <q-img :src="distributionGraphs[1].image" fit="scale-down"></q-img>
              </q-carousel-slide>
              <q-carousel-slide :name="3" class="column ">
                <q-img :src="distributionGraphs[2].image" fit="scale-down"></q-img>
              </q-carousel-slide>
            </q-carousel>
          </div>
        </div>
        <div class="row">
          <div class="col-md-4 col-xs-12">
            <el-table :data="distributionData.genes_amps" style="width: 120%" :show-header="false">
              <el-table-column prop="number" label="Number">
                <template #default="props">
                  {{ props.row.number }}
                  {{ props.row.type }}
<!--                  <a href="/browse_data" style="font-size: medium"> {{ props.row.number }}</a>-->
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="col-md-4 col-xs-12">
            <!--              <h4>Derived from</h4>-->
            <el-table :data="distributionData.families_habitats" style="width: 120%" :show-header="false">
              <el-table-column prop="number" label="Number">
                <template #default="props">
                  {{ props.row.number }}
                  {{ props.row.type }}
<!--                  <a href="/browse_data" style="font-size: medium"> {{ props.row.number }}</a>-->
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="col-md-4 col-xs-12">
            <el-table :data="distributionData.genomes_metagenomes" style="width: 120%" :show-header="false">
              <el-table-column prop="number" label="Number">
                <template #default="props">
                  {{ props.row.number }}
                  {{ props.row.type }}
<!--                  <a href="/browse_data" style="font-size: medium"> {{ props.row.number }}</a>-->
                </template>
              </el-table-column>
            </el-table>
          </div>
        </div>
      </div>
      <div class="col-12 col-md-5 col-xl-3 justify-center q-pl-md q-ma-auto">
        <div class="row">
          <div class="col-12 text-center subsubsection-title-center q-mb-md">Search by sequence</div>
          <div class="col-12">
            <div class="main-text">
              Paste (&leq; 10) peptide sequences here (FASTA format).
            </div>
          </div>
          <div class="col-12">
            <q-input class="q-my-md" v-model="sequences" filled clearable type="textarea" color="primary"
                     label="Please enter up to 10 sequences" rows="20"/>
<!--            TODO update here.-->
<!--            <q-uploader-->
<!--                style="max-width: 300px"-->
<!--                url="http://localhost:4444/upload"-->
<!--                label="Upload a fasta file."-->
<!--                max-files="1"-->
<!--                @rejected="onRejected"-->
<!--            />-->

            <!--            <q-input-->
<!--                type="textarea" :rows="20" :autosize="{ minRows: 15, maxRows: 20}" :placeholder="exampleSequences" v-model="sequences">-->
<!--            </q-input>-->
            <div class="text-bold">
              Search for:
              <q-btn-toggle
                  v-model="searchMethod" no-caps rounded unelevated toggle-color="primary" color="white"
                  text-color="primary" size="sm"
                  :options="[{label: 'AMPs', value: 'MMseqs', slot: 'MMseqs'}, {label: 'Families', value: 'HMMER', slot: 'HMMER'}]" />
<!--              <el-radio v-model="searchMethod" label="MMseqs">AMPs</el-radio>-->
<!--              <el-radio v-model="searchMethod" label="HMMER">Families</el-radio>-->
              <q-btn @click="sequenceSearch" label="Submit" class="bg-primary text-white" />
            </div>
            <div class="main-text">
              <span class="text-bold">Note</span>: For large queries and offline use, please download
              <a href="https://ampsphere.big-data-biology.org/downloads"> our prebuilt indices</a>.
            </div>
          </div>
        </div>
      </div>
      <div class="col-0 col-xl-2 bg-white"></div>
    </div>
  </div>
</template>

<style>
.env-logos {
  width: 100%;
  height: 100px;
  background-color: #ffffff;
}
</style>

<script>
import {ref} from "vue"
import {useQuasar} from 'quasar'


export default {
  name: 'Home',
  data() {
    return {
      slideIndex: ref(1),
      statistics: {
        num_genes: 5513556,
        num_amps: 863498,
        num_families: 8535,
        num_habitats: 73,
        num_genomes: 47803,
        num_metagenomes: 61393
      },
      distributionData: {
        genes_amps: [
          {number: (5513556).toLocaleString('en-US'), type: 'genes'},
          {number: (863498).toLocaleString('en-US'), type: 'AMPs'},
        ],
        genomes_metagenomes: [
          {number: (47803).toLocaleString('en-US'), type: 'genomes'},
          {number: (61393).toLocaleString('en-US'), type: 'metagenomes'}
        ],
        families_habitats: [
          {number: (8535).toLocaleString('en-US'), type: 'families'},
          {number: (73).toLocaleString('en-US'), type: 'habitats'},
        ]
      },
      distributionGraphs: [
        {type: 'geography', image: require('../assets/geographical_distribution.svg')},
        {type: 'microbial_source', image: require('../assets/microbial_source_distribution.svg')},
        {type: 'habitat', image: require('../assets/habitat_distribution.svg')},
      ],
      // exampleSequences:
      //     ">AMP10.000_002 | SPHERE-III.010_054\n" +
      //     "KRVKSFFKGYMRAIEINAALMYGYRPK\n" +
      //     ">AMP10.000_003 | SPHERE-III.001_008\n" +
      //     "GRVIGKQGRIAKAIRVVMRAAAVRVDEKVLVEID\n",
      searchMethod: 'MMseqs',
      sequences:
          ">AMP10.000_002 | SPHERE-III.010_054\n" +
          "KRVKSFFKGYMRAIEINAALMYGYRPK\n" +
          ">AMP10.000_003 | SPHERE-III.001_008\n" +
          "GRVIGKQGRIAKAIRVVMRAAAVRVDEKVLVEID\n",
      // fastaFile: [],
    }
  },
  setup() {
  },
  mounted() {
    this.retrieveStatistics()
  },
  methods: {
    handleFamilyDetail(accession) {
      console.log('goto', '/family?accession=' + accession)
      window.open('/family?accession=' + accession)
    },
    handleBrowse(by) {
      window.open('/browse_data?by=' + by)
    },
    retrieveStatistics() {
      var self = this
      this.axios.get('/statistics', {})
          .then(function (response) {
            console.log(response.data)
            self.statistics = response.data
            self.distributionData =  {
              genes_amps: [
                {number: self.statistics.num_genes.toLocaleString('en-US'), type: 'genes'},
                {number: self.statistics.num_amps.toLocaleString('en-US'), type: 'AMPs'},
              ],
              genomes_metagenomes: [
                {number: self.statistics.num_genomes.toLocaleString('en-US'), type: 'genomes'},
                {number: self.statistics.num_metagenomes.toLocaleString('en-US'), type: 'metagenomes'}
              ],
              families_habitats: [
                {number: self.statistics.num_families.toLocaleString('en-US'), type: 'families'},
                {number: self.statistics.num_habitats.toLocaleString('en-US'), type: 'habitats'},
              ]
            }
            // self.distributionData = {
            //   amps_families: [
            //     {number: self.statistics.num_amps.toLocaleString('en-US'), type: 'AMPs'},
            //     {number: self.statistics.num_families.toLocaleString('en-US'), type: 'families'},
            //   ],
            //   genomes_metagenomes: [
            //     {number: self.statistics.num_genomes.toLocaleString('en-US'), type: 'genomes'},
            //     {number: self.statistics.num_metagenomes.toLocaleString('en-US'), type: 'metagenomes'}
            //   ],
            //   habitats_hosts: [
            //     {number: self.statistics.num_habitats.toLocaleString('en-US'), type: 'habitats'},
            //     {number: self.statistics.num_hosts.toLocaleString('en-US'), type: 'hosts'},
            //   ]
            // }
          })
          .catch(function (error) {
            console.log(error);
          });
    },
    sequenceSearch() {
      if ((this.sequences.match(/\n/g) || '').length + 1 > 20) {
        this.$message('Please input up to 10 sequences.')
      } else {
        window.open(encodeURI('/sequence_search?method=' + this.searchMethod + '&queries=' + this.sequences), '_self')
      }
    },
  }
}
</script>
