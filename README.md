import { useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button"; import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select";

const blocos = ["A1", "A2", "B1", "B2"];

const App = () => { const [produtos, setProdutos] = useState([]); const [form, setForm] = useState({ bloco: "", cultivar: "", categoria: "", lote: "" }); const [editIndex, setEditIndex] = useState(null); const [filtroCategoria, setFiltroCategoria] = useState(""); const [buscaLote, setBuscaLote] = useState("");

const adicionarProduto = () => { if (form.bloco && form.cultivar && form.categoria && form.lote) { if (editIndex !== null) { const atualizados = [...produtos]; atualizados[editIndex] = form; setProdutos(atualizados); setEditIndex(null); } else { setProdutos([...produtos, form]); } setForm({ bloco: "", cultivar: "", categoria: "", lote: "" }); } };

const editarProduto = (produto, index) => { setForm(produto); setEditIndex(index); };

const removerProduto = index => { const confirmacao = window.confirm("Tem certeza que deseja remover este produto?"); if (confirmacao) { const atualizados = produtos.filter((_, i) => i !== index); setProdutos(atualizados); } };

const produtosFiltrados = produtos .map((p, i) => ({ ...p, index: i })) .filter(p => (!filtroCategoria || p.categoria === filtroCategoria) && (!buscaLote || p.lote.toLowerCase().includes(buscaLote.toLowerCase())) );

const produtosPorBloco = blocos.reduce((acc, bloco) => { acc[bloco] = produtosFiltrados.filter(p => p.bloco === bloco); return acc; }, {});

const categoriasUnicas = Array.from(new Set(produtos.map(p => p.categoria)));

return ( <div className="p-4 space-y-6"> <h1 className="text-2xl font-bold">Organizador de Câmara Fria</h1>

<Card>
    <CardContent className="space-y-2 pt-4">
      <Select value={form.bloco} onValueChange={value => setForm({ ...form, bloco: value })}>
        <SelectTrigger>
          <SelectValue placeholder="Selecione o bloco" />
        </SelectTrigger>
        <SelectContent>
          {blocos.map(bloco => (
            <SelectItem key={bloco} value={bloco}>{bloco}</SelectItem>
          ))}
        </SelectContent>
      </Select>

      <Input
        placeholder="Cultivar"
        value={form.cultivar}
        onChange={e => setForm({ ...form, cultivar: e.target.value })}
      />
      <Input
        placeholder="Categoria"
        value={form.categoria}
        onChange={e => setForm({ ...form, categoria: e.target.value })}
      />
      <Input
        placeholder="Lote"
        value={form.lote}
        onChange={e => setForm({ ...form, lote: e.target.value })}
      />
      <Button onClick={adicionarProduto}>{editIndex !== null ? "Salvar Edição" : "Adicionar"}</Button>
    </CardContent>
  </Card>

  <div className="grid grid-cols-2 gap-4">
    <Card>
      <CardContent className="space-y-2 pt-4">
        <Select value={filtroCategoria} onValueChange={setFiltroCategoria}>
          <SelectTrigger>
            <SelectValue placeholder="Filtrar por categoria" />
          </SelectTrigger>
          <SelectContent>
            <SelectItem value="">Todas</SelectItem>
            {categoriasUnicas.map(cat => (
              <SelectItem key={cat} value={cat}>{cat}</SelectItem>
            ))}
          </SelectContent>
        </Select>
        <Input
          placeholder="Buscar por lote"
          value={buscaLote}
          onChange={e => setBuscaLote(e.target.value)}
        />
      </CardContent>
    </Card>

    {blocos.map(bloco => (
      <Card key={bloco} className="bg-blue-50">
        <CardContent className="pt-4">
          <h2 className="font-semibold text-lg">Bloco {bloco}</h2>
          <ul className="mt-2 space-y-2">
            {produtosPorBloco[bloco]?.length > 0 ? (
              produtosPorBloco[bloco].map((p, idx) => (
                <li key={idx} className="text-sm flex justify-between items-center">
                  <div>
                    <span className="font-medium">{p.cultivar}</span> ({p.categoria}) - Lote {p.lote} - Bloco {p.bloco}
                  </div>
                  <div className="space-x-2">
                    <Button size="sm" onClick={() => editarProduto(p, p.index)}>Editar</Button>
                    <Button size="sm" variant="destructive" onClick={() => removerProduto(p.index)}>Remover</Button>
                  </div>
                </li>
              ))
            ) : (
              <li className="text-sm text-gray-500">Sem produtos</li>
            )}
          </ul>
        </CardContent>
      </Card>
    ))}
  </div>
</div>

); };

export default App;
