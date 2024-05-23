este codigo se trata de un automata finito con 9 estados
el proposito de este programa es aceptar numeros de 4 bits en binario 
los numeros que acepta el automata son del 0 al 7 siendo estos una cancion 
diferente por numero, tambien para hacer mas visible la traslacion de los 
estados hasta el de aceptacion este proporciona ventanas emergentes, avisando 
al usuario si la cadena coresponde a un numero binario y si fue aceptado 

a continuacion se muestra el codigo

using System;
using System.Collections.Generic;
using System.Windows.Forms;
using System.Media;
using System.IO;

namespace proyecto
{
    public abstract class Estado
    {
        public abstract Estado ProcesarEntrada(string entrada);
        public abstract void EjecutarAccion();
    }
    public class EstadoInicial : Estado
    {
        private static string[] canciones = {
            "sonidos/Queen - We Are The Champions (Official Video).wav",
            "sonidos/ytmp3free.cc_dragon-ball-z-melodia-de-tapion-full-no-rap-youtubemp3free.org.wav",
            "sonidos/ytmp3free.cc_la-cancion-originanal-de-la-pantera-rosa-youtubemp3free.org.wav",
            "sonidos/ytmp3free.cc_cajita-musical-youtubemp3free.org.wav",
            "sonidos/mp3free.cc_caja-musical-melodia-youtubemp3free.org.wav",
            "sonidos/ytmp3free.cc_cajita-de-musica-naruto-youtubemp3free.org.wav",
            "sonidos/ytmp3free.cc_cajita-musical-de-el-viaje-de-chihiro-youtubemp3free.org.wav",
            "sonidos/ytmp3free.cc_elfen-lied-lilium-music-box-hd-youtubemp3free.org.wav"
        };

        public override Estado ProcesarEntrada(string entrada)
        {
            if (entrada.Length == 4 && EsBinario(entrada))
            {
                int index = Convert.ToInt32(entrada, 2);
                if (index >= 0 && index < canciones.Length)
                {
                    MessageBox.Show("Transición: Estado Q0 -> Estado Qf");
                    return new EstadoReproduciendo(index);
                }
            }

            MessageBox.Show("Transición: Estado Q0 -> Estado Q0 (numero binario inválido o error en los caracteres ingresados)");
            return this;
        }

        public override void EjecutarAccion()
        {

        }
        private bool EsBinario(string cadena)
        {
            foreach (char c in cadena)
            {
                if (c != '0' && c != '1')
                {
                    return false;
                }
            }
            return true;
        }
        public static string[] ObtenerCanciones()
        {
            return canciones;
        }
    }
    public class EstadoReproduciendo : Estado
    {
        private int cancionIndex;

        public EstadoReproduciendo(int index)
        {
            cancionIndex = index;
        }

        public override Estado ProcesarEntrada(string entrada)
        {
            MessageBox.Show("Transición: Estado Qf -> Estado Q0");
            return new EstadoInicial();
        }

        public override void EjecutarAccion()
        {
            string[] canciones = EstadoInicial.ObtenerCanciones();

            try
            {
                if (cancionIndex >= 0 && cancionIndex < canciones.Length)
                {
                    string basePath = Directory.GetCurrentDirectory();
                    string fullPath = Path.Combine(basePath, canciones[cancionIndex]);

                    using (SoundPlayer player = new SoundPlayer(fullPath))
                    {
                        player.Play();
                        MessageBox.Show("Esta es la canción de esta cadena.");
                        player.Stop();
                    }
                }
                else
                {
                    MessageBox.Show("Por favor, ingrese un número válido.");
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Error al reproducir la música: " + ex.Message);
            }
        }
    }

    public partial class MainForm : Form
    {
        private Estado estadoActual;

        public MainForm()
        {
            InitializeComponent();
            estadoActual = new EstadoInicial();
        }

        private void Button1Click(object sender, EventArgs e)
        {
             string textonumero = textBox1.Text;

        Estado nuevoEstado = estadoActual.ProcesarEntrada(textonumero);
        nuevoEstado.EjecutarAccion();
        estadoActual = nuevoEstado;
            
        }
    }
}
