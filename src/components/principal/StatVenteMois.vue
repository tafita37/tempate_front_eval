<script setup>
    import GeneralContain from '../reeuse/GeneralContain.vue';
    import HeaderOne from '../reeuse/HeaderOne.vue';
    import MenuDeroulantOne from '../reeuse/MenuDeroulantOne.vue';
    import SousGeneralContain from '../reeuse/SousGeneralContain.vue';
    import PrincipalContain from '../reeuse/PrincipalContain.vue';
    import SousPrincipal from '../reeuse/SousPrincipal.vue';
    import FooterOne from '../reeuse/FooterOne.vue';
    import H4CardTitle from '../reeuse/H4CardTitle.vue';
    import CardDescription from '../reeuse/CardDescription.vue';
    import TableHead from '../reeuse/TableHead.vue';
    import TableContain from '../reeuse/TableContain.vue';
    import axios from 'axios';
    import { onMounted, reactive, ref } from 'vue';
    import { baseUrl } from '@/static/Constantes';
    import { Bar } from 'vue-chartjs';
    import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'
    import NormalSearch from '../reeuse/NormalSearch.vue';
    import html2pdf from 'html2pdf.js';
    import html2canvas from 'html2canvas';
    import { jsPDF } from 'jspdf';

    // const exportToPDF = () => {
    //     // Sélectionner le contenu à exporter
    //     const contentToExport = document.querySelector('.generalContain');

    //     // Options pour html2pdf
    //     const options = {
    //         margin: 1,
    //         filename: 'mon_document.pdf',
    //         image: { type: 'jpeg', quality: 0.98 },
    //         html2canvas: { dpi: 192, letterRendering: true },
    //         jsPDF: { unit: 'in', format: 'letter', orientation: 'portrait' }
    //     };

    //     // Utiliser html2pdf pour exporter en PDF
    //     html2pdf().from(content).set(options).save();

    //     console.log("test");
    // };

    const exportToPDF = () => {
        try {
            // // Sélectionner le contenu à exporter
            // const contentToExport = document.querySelector('.generalContain');

            // // Options pour html2pdf
            // const options = {
            //     filename: 'mon_document.pdf',
            //     jsPDF: { unit: 'in', format: 'letter', orientation: 'portrait' }
            // };

            // // Utiliser html2pdf pour exporter en PDF
            // html2pdf().from(contentToExport).set(options).save();
            html2pdf(document.querySelector('.generalContain'), {
                margin : 1,
                filename : 'test.pdf'
            })
        } catch (error) {
            console.error(error);
        }
    };

    const allStat=reactive({});
    const chartOptions= {responsive: true};
    const annee=reactive({});
    
    ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale);

    const chartData = reactive({
        labels: ['Janvier', 'Fevrier', 'Mars', 'Avril', 'Mai', 'Juin', 'Juillet', 'Aout', 'Septembre', 'Octobre', 'Novembre', 'Decembre'],
        datasets: [ 
            {
                label : "Quantite vendu", 
                backgroundColor: '#f87979',
                data: [] 
            } 
        ]
    });

    const getAllStat = async () => {
        try {
            // chartData.datasets[0].data.splice(0, chartData.datasets[0].data.length);
            const token=localStorage.getItem("tokenMagasin");
            const headers = {
                Authorization: `Bearer ${token}`,
            };
            const urlApiSpringBoot = baseUrl+'/laptop/statVenteMois';
            const params=new URLSearchParams();
            if(annee.annee) {
                params.append("annee", annee.annee);
            } else {
                // params.append("annee", 2023);
                params.append("annee", new Date().getFullYear());
            }
            const response = await axios.get(urlApiSpringBoot, { params, headers });
            Object.assign(allStat, response.data);
            if(allStat.data.length==0) {    
                for(let i=0; i<12; i++) {
                    chartData.datasets[0].data[i]=0;
                }
            }
            for(let i=0; i<allStat.data.length; i++) {
                chartData.datasets[0].data[i]=allStat.data[i].quantiteLaptop;
            }
            console.log(chartData.datasets[0].data);
        } catch (error) {
            console.log(error);
            if(error.response.status==403) {
                router.push("/");
            }
        }
    };

    onMounted(() => {
        getAllStat();
    });
</script>
<template>
    <GeneralContain class="">
        <HeaderOne />
        <SousGeneralContain>
            <MenuDeroulantOne />
            <PrincipalContain>
                <SousPrincipal>
                    <NormalSearch place-holder-search="Annee" :search-function="() => getAllStat()" :reactive-object="annee" reactive-reference="annee"/>
                    <div class="col-lg-6 grid-margin stretch-card mx-auto special_stat generalContain">
                        <div class="card">
                            <div class="card-body">
                                <H4CardTitle card-title="Vente"/>
                                <CardDescription card-description="Statistique"/>
                                <div class="table-responsive">
                                    <table class="table">
                                        <TableHead :title-header="['Quantite', 'Mois', 'Annee']"/>
                                        <tbody>
                                            <TableContain v-for="stat in allStat.data" 
                                            :liste-elements="[stat.quantiteLaptop, stat.mois, stat.annee]"/>
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                        </div>
                        <Bar
                            id="my-chart-id"
                            :options="chartOptions"
                            :data="chartData"
                        />
                    </div>
                </SousPrincipal>
                <FooterOne />
            </PrincipalContain>
        </SousGeneralContain>
    <button @click="exportToPDF">Exporter en PDF</button>
    </GeneralContain>
    <!-- <div ref="contentToExport">
      <h1>Mon Document PDF</h1>
      <p>Ceci est un exemple de contenu à exporter en PDF.</p>
    </div> -->
</template>
<style scoped>
    @import "@/assets/css/reeuse/liste.css";

    #my-chart-id {
        width: auto  !important;
        height: auto !important;
    }

    .special_stat {
        display: flex;
        flex-direction: column;
        gap: 75px;
        margin-left: 0 !important;
        height: auto;
    }
</style>