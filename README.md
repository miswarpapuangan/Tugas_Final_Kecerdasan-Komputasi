# Tugas_Final_Kecerdasan-Komputasi
>>  Representasi kasus Travelling Salesman Problem (TSP) menggunakan teknik Strategi Evolusioner (ES) untuk mencari rute terpendek.
>> Program ini dibuat menggunakan bahasa pemrograman Delphi 2010. Menggunakan sistem operasi Windows 8 32 Bit dengan langkah-langkah sebagai berikut:

unit TSP_SE;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, StdCtrls, Grids, ExtCtrls;

type
  TForm1 = class(TForm)
    Button1: TButton;
    Label1: TLabel;
    GroupBox1: TGroupBox;
    grid: TStringGrid;
    GroupBox2: TGroupBox;
    Image1: TImage;
    GroupBox3: TGroupBox;
    tabel: TStringGrid;
    procedure Button1Click(Sender: TObject);
    procedure BentukKromosomAwal;
    procedure Mutasi;
    procedure ClearTable;
    procedure TampilOffspring;
    procedure Gabung;
    procedure HitungFitness;
    procedure Seleksi;
    procedure FormCreate(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

var
  Form1: TForm1;
  matrik_cost_kota : array[1..5,1..5] of real;
  kromosom : array[1..5,1..5] of integer;
  Offspring : array[1..50,1..5] of integer;
  parent_temp : array[1..5] of integer;
  fitnessKrom : array[1..100] of real;
  Myu, lamda, Sigma : integer;
  urutan : integer;
  cost_terbaik : real;

implementation

{$R *.dfm}

procedure TForm1.BentukKromosomAwal;
var m, p, y, acak : integer;
    jumpa : string ;
begin
    for m:= 1 to Myu do //Upop adalah ukuran populasi
    begin
        for p := 1 to 5 do // 5 adalah jumlah kota
        begin
            jumpa := 'sama';
            while jumpa = 'sama' do
            begin
                jumpa := 'tidak sama';
                randomize;
                acak := random(5) + 5;
                if acak > 5 then acak := acak - 5;

                for y := 1 to 5 do
                begin
                    if acak = kromosom[m,y] then
                        jumpa := 'sama';
                        //showmessage('sama');
                end;
            end;

            kromosom[m,p] := acak;
            //tabel.Cells[p,m] := inttostr(kromosom[m,p]);
        end;
    end;
end;

procedure TForm1.Button1Click(Sender: TObject);
var uji, i : integer;
begin
    // misalkan cost antara kota X dan kota Y adalah sebagai berikut
    matrik_cost_kota[1,1] := 0;
    matrik_cost_kota[1,2] := 10;
    matrik_cost_kota[1,3] := 15;
    matrik_cost_kota[1,4] := 25;
    matrik_cost_kota[1,5] := 5;

    matrik_cost_kota[2,1] := 10;
    matrik_cost_kota[2,2] := 0;
    matrik_cost_kota[2,3] := 20;
    matrik_cost_kota[2,4] := 3;
    matrik_cost_kota[2,5] := 7;

    matrik_cost_kota[3,1] := 15;
    matrik_cost_kota[3,2] := 20;
    matrik_cost_kota[3,3] := 0;
    matrik_cost_kota[3,4] := 12;
    matrik_cost_kota[3,5] := 13;

    matrik_cost_kota[4,1] := 25;
    matrik_cost_kota[4,2] := 3;
    matrik_cost_kota[4,3] := 12;
    matrik_cost_kota[4,4] := 0;
    matrik_cost_kota[4,5] := 8;

    matrik_cost_kota[5,1] := 5;
    matrik_cost_kota[5,2] := 7;
    matrik_cost_kota[5,3] := 13;
    matrik_cost_kota[5,4] := 8;
    matrik_cost_kota[5,5] := 0;

    //--------------------------------------------------------------inisialisasi
    Myu := 5;
    Lamda := 3;
    // Sigma ditentukan secara random dalam proses mutasi
    BentukKromosomAwal;
    //--------------------------------------------------------------------------
    uji := 1;
    while uji < 5 do       // misalkan 5 adalah kondisi berhenti
    begin
        Mutasi;
        HitungFitness;
        //TampilOffspring;
        Seleksi;

        uji := uji + 1;
    end;     //end while

    clearTable;
    for i := 1 to 5 do
        tabel.Cells[i,1] := inttostr(kromosom[1,i]);

    tabel.Cells[7,1] := floattostr(cost_terbaik);
end;

procedure TForm1.ClearTable;
var x, y : integer;
begin
    for x := 1 to 5 do
    begin
        for y := 1 to 15 do
        begin
            tabel.Cells[x,y] := '';
        end;
    end;
end;

procedure TForm1.Mutasi;
var parent, child, mut, x, y, i, temp : integer;
    pos_off : integer;
begin
    for parent := 1 to Myu do      //Myu = ukuran populasi
    begin

        for i:= 1 to 5 do//------------------------ambil individu sebagai parent
            parent_temp[i] := kromosom[parent,i];

        for child := 1 to Lamda do//---------Lamda = banyaknya anak per-individu
        begin
            randomize;
            Sigma := random(3); // Sigma = berapa kali individu dimutasi untuk menghasilkan satu chil
            if Sigma = 0 then Sigma := 1;

            for mut := 1 to Sigma do//---lakukan mutasi pada individu sebanyak Sigma
            begin
                randomize;
                x := random(5);
                if x = 0 then x := 1;

                randomize;
                y := random(5);
                if y = 0 then y := 1;
                while y = x do
                begin
                    y := random(5)+3;
                    if y > 5 then y := y-5;
                end;

                //----------tukar posisi gen sehingga menghasilkan individu baru
                temp := parent_temp[x];
                parent_temp[x] := parent_temp[y];
                parent_temp[y] := temp;
            end;
            //--------------------------------simpan individu baru kedalam array
            pos_off := 1;
            while offspring[pos_off,1] <> 0 do
                pos_off := pos_off + 1;

            for i := 1 to 5 do
                offspring[pos_off,i] := parent_temp[i]
        end;
    end;
end;

procedure TForm1.TampilOffspring;
var no, kolom : integer;
begin
    cleartable;
    for no := 1 to 20 do
    begin
        for kolom := 1 to 5 do
        begin
            tabel.Cells[kolom,no] := inttostr(offspring[no,kolom]);
        end;
        tabel.Cells[7,no] := floattostr(FitnessKrom[no]);
    end;


end;

procedure TForm1.FormCreate(Sender: TObject);
var i, j, uji : integer;
begin
    for i := 1 to 5 do
    begin
        grid.Cells[0,i] := inttostr(i);
        grid.Cells[i,0] := inttostr(i);
    end;

    tabel.Cells[7,0] := 'cost';

    // misalkan cost antara kota x dan kota Y adalah sebagai berikut
    matrik_cost_kota[1,1] := 0;
    matrik_cost_kota[1,2] := 10;
    matrik_cost_kota[1,3] := 15;
    matrik_cost_kota[1,4] := 25;
    matrik_cost_kota[1,5] := 5;

    matrik_cost_kota[2,1] := 10;
    matrik_cost_kota[2,2] := 0;
    matrik_cost_kota[2,3] := 20;
    matrik_cost_kota[2,4] := 3;
    matrik_cost_kota[2,5] := 7;

    matrik_cost_kota[3,1] := 15;
    matrik_cost_kota[3,2] := 20;
    matrik_cost_kota[3,3] := 0;
    matrik_cost_kota[3,4] := 12;
    matrik_cost_kota[3,5] := 13;

    matrik_cost_kota[4,1] := 25;
    matrik_cost_kota[4,2] := 3;
    matrik_cost_kota[4,3] := 12;
    matrik_cost_kota[4,4] := 0;
    matrik_cost_kota[4,5] := 8;

    matrik_cost_kota[5,1] := 5;
    matrik_cost_kota[5,2] := 7;
    matrik_cost_kota[5,3] := 13;
    matrik_cost_kota[5,4] := 8;
    matrik_cost_kota[5,5] := 0;

    for i := 1 to 5 do
    begin
        for j := 1 to 5 do
            grid.Cells[j,i] := floattostr(matrik_cost_kota[i,j]);
    end
end;

procedure TForm1.Gabung;
var no, norut, i, data : integer;
begin
    //gabungkan parent dan offspring
    norut := (Myu * Lamda) + 1;
    no := 1;
    for norut := 1 + (Myu * Lamda) to ((Myu * Lamda)+Myu) do
    begin
        //gabung parent kedalam offspring
        for i:= 1 to 5 do
        begin
            data := kromosom[no,i];
            offspring[norut,i] := data;
            //showmessage(inttostr(offspring[norut,i]));
        end;
        no := no + 1;
        //showmessage(inttostr(norut));
    end;
end;

procedure TForm1.HitungFitness;
var baris, kolom, kotaX, kotaY : integer;
    totalCost : real;
begin
    Gabung;
    for baris := 1 to ((Myu * Lamda)+ Myu) do
    begin
        totalCost := 0;
        for kolom := 1 to 5-1 do
        begin
            kotaX := offspring[baris,kolom];
            kotaY := offspring[baris,kolom+1];

            totalCost := totalCost + matrik_cost_kota[kotaX,kotaY];
        end;
        fitnessKrom[baris] := totalCost;
    end;
end;

procedure TForm1.Seleksi;
var individu, i, j : integer;
    min_cost : real;
begin
    for individu := 1 to Myu do
    begin
        min_cost := 1000;
        for j:= 1 to (Myu + (Myu * Lamda)) do
        begin
            if min_cost > FitnessKrom[j] then
            begin
                min_cost := FitnessKrom[j];
                urutan := j;
            end;
        end;

        //Pilih individu dan simpan
        for i:= 1 to 5 do
        begin
            kromosom[individu, i] := offspring[urutan, i];
            if i = 1 then cost_terbaik := min_cost;
            FitnessKrom[urutan] := 1000;    // mengubah nilai menjadi 1000 bermaksud menghapus krom dari list karena sudah terpilih
            tabel.Cells[i,individu+ 21] := inttostr(kromosom[individu, i]);
            tabel.Cells[7,individu+ 21] := floattostr(min_cost);
        end;
    end;
end;

end.
